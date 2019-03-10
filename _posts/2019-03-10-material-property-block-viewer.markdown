---
layout: post
title:  "material property block viewer"
date:   2019-03-10 00:10:35
categories: [unity]
---
It's a tool to view or edit the MaterialPropertyBlock of the renderer component.

- Get Material Property

  MaterialProperty[] props = MaterialEditor.GetMaterialProperties(new Material[] { mat }); 

```c#
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[ExecuteInEditMode]
[RequireComponent(typeof(Renderer))]
public class MPBViewer : MonoBehaviour
{
    #region Data

    public Renderer m_rd;
    public readonly List<Material> m_sharedMaterialList = new List<Material>();
    public List<MaterialPropertyBlock> m_blockList = new List<MaterialPropertyBlock>();
    public List<bool> m_foldoutList = new List<bool>();

    public bool m_autoRefresh = false;
    private int m_autoRefreshCnt = 0;
    public int m_autoRefreshFreq = 10;

    #endregion

    #region Function

    #region Read MaterialPropertyBlock
    private void InnerRefresh()
    {
        m_blockList.Clear();
        m_sharedMaterialList.Clear();

        m_rd = GetComponent<Renderer>();
        if(m_rd == null)
        {
            Log("No Renderer Component Found");
            return;
        }

        Material[] mats = m_rd.sharedMaterials;
        if (mats == null || mats.Length == 0)
        {
            Log("No Shared Material Found");
            return;
        }

        for (int i = 0; i < mats.Length; i++)
        {
            Material mat = mats[i];
            MaterialPropertyBlock block = new MaterialPropertyBlock();
            m_rd.GetPropertyBlock(block, i);
            m_sharedMaterialList.Add(mat);
            m_blockList.Add(block);
        }

        if(m_sharedMaterialList.Count != m_foldoutList.Count)
        {
            m_foldoutList.Clear();
            for (int i = 0; i < m_sharedMaterialList.Count; i++)
            {
                m_foldoutList.Add(false);
            }
        }
    }

    public void Refresh()
    {
        try
        {
            InnerRefresh();
        }
        catch (Exception e)
        {
            Log("Refresh Unknown Error");
            Log(e.Message);
        }
    }
    #endregion
    
    #region Modify MaterialPropertyBlock
    public void SetValue(int index, string name, Color value)
    {
        m_blockList[index].SetColor(name, value);
        m_rd.SetPropertyBlock(m_blockList[index], index);
    }

    public void SetValue(int index, string name, Vector4 value)
    {
        m_blockList[index].SetVector(name, value);
        m_rd.SetPropertyBlock(m_blockList[index], index);
    }

    public void SetValue(int index, string name, float value)
    {
        m_blockList[index].SetFloat(name, value);
        m_rd.SetPropertyBlock(m_blockList[index], index);
    }

    public void SetValue(int index, string name, Texture value)
    {
        m_blockList[index].SetTexture(name, value);
        m_rd.SetPropertyBlock(m_blockList[index], index);
    }
    #endregion

    #region LiftCycle
    void Update()
    {
        if (m_autoRefresh)
        {
            m_autoRefreshCnt++;
            if (m_autoRefreshCnt > m_autoRefreshFreq)
            {
                Refresh();
                m_autoRefreshCnt = 0;
            }
        }
    }
    #endregion

    #region Utility
    private void Log(string msg)
    {
        Debug.Log("GameObject:" + gameObject.name + ". " + "MPBViewer: " + msg + ".");
    }
    #endregion

    #endregion
}

```
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;

namespace UnityEditor
{
    [CustomEditor(typeof(MPBViewer))]
    public class MPBViewerInspector : Editor
    {
        private MPBViewer m_viewer;

        public void OnEnable()
        {
            m_viewer = (MPBViewer)target;
        }

        private string GetName(string displayName, string name)
        {
            return displayName + " [" + name + "]";
        }

        private void ShowPropertyBlock(int index, Material mat, MaterialPropertyBlock block)
        {
            #region check paramter
            if (mat == null)
            {
                EditorGUILayout.LabelField("mat is null.");
                return;
            }
            if (block == null)
            {
                EditorGUILayout.LabelField("block is null.");
                return;
            }
            #endregion

            MaterialProperty[] props = MaterialEditor.GetMaterialProperties(new Material[] { mat });
            for (var i = 0; i < props.Length; i++)
            {
                MaterialProperty prop = props[i];
                string name = prop.name;
                string displayName = prop.displayName;
                MaterialProperty.PropType type = prop.type;
                switch (type)
                {
                    case MaterialProperty.PropType.Color:
                        {
                            Color value = block.GetColor(name);
                            Color newValue = EditorGUILayout.ColorField(GetName(displayName, name), value);
                            if (newValue != value)
                            {
                                m_viewer.SetValue(index, name, newValue);
                            }
                        }
                        break;
                    case MaterialProperty.PropType.Vector:
                        {
                            Vector4 value = block.GetVector(name);
                            Vector4 newValue = EditorGUILayout.Vector4Field(GetName(displayName, name), value);
                            if (newValue != value)
                            {
                                m_viewer.SetValue(index, name, newValue);
                            }
                        }
                        break;
                    case MaterialProperty.PropType.Float:
                        {
                            float value = block.GetFloat(name);
                            float newValue = EditorGUILayout.FloatField(GetName(displayName, name), value);
                            if (newValue != value)
                            {
                                m_viewer.SetValue(index, name, newValue);
                            }
                        }
                        break;
                    case MaterialProperty.PropType.Range:
                        {
                            float value = block.GetFloat(name);
                            float newValue = EditorGUILayout.FloatField(GetName(displayName, name), value);
                            if (newValue != value)
                            {
                                m_viewer.SetValue(index, name, newValue);
                            }
                        }
                        break;
                    case MaterialProperty.PropType.Texture:
                        {
                            Texture value = block.GetTexture(name);
                            Texture newValue = EditorGUILayout.ObjectField(
                                GetName(displayName, name), value, typeof(Texture), true) as Texture;
                            if (newValue != value)
                            {
                                m_viewer.SetValue(index, name, newValue);
                            }
                        }
                        break;
                    default:
                        {
                        }
                        break;
                }
            }
        }

        public override void OnInspectorGUI()
        {
            #region auto refresh
            m_viewer.m_autoRefresh = EditorGUILayout.Toggle("Auto Refresh", m_viewer.m_autoRefresh);
            if(m_viewer.m_autoRefresh)
            {
                m_viewer.m_autoRefreshFreq = EditorGUILayout.IntField("Auto Refresh Freq", m_viewer.m_autoRefreshFreq);
            }
            else
            {
                if (GUILayout.Button("Refresh"))
                {
                    m_viewer.Refresh();
                }
            }
            #endregion

            #region mpb info
            List<Material> mats = m_viewer.m_sharedMaterialList;
            List<MaterialPropertyBlock> blocks = m_viewer.m_blockList;
            for (int i = 0; i < mats.Count; i++)
            {
                m_viewer.m_foldoutList[i] = EditorGUILayout.Foldout(m_viewer.m_foldoutList[i], mats[i].name);
                if (m_viewer.m_foldoutList[i])
                {
                    ShowPropertyBlock(i, mats[i], blocks[i]);
                }
            }
            #endregion

            serializedObject.ApplyModifiedProperties();
        }
    }

}
```



![MPBViewer]({{ site.baseurl }}/images/material-property-block-viewer/mpb_viewer.png)