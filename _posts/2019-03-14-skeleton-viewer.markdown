---
layout: post
title:  "skeleton viewer"
date:   2019-03-14 00:20:23
categories: [unity]
---
### Skeleton Viewer

![skeleton_viewer]({{ site.baseurl }}/images/skeleton-viewer/skeleton_viewer.png)

![skeleton_viewer2]({{ site.baseurl }}/images/skeleton-viewer/skeleton_viewer2.png)

### Code

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SkeletonViewer : MonoBehaviour
{
    public Color m_boneColor = Color.blue;
    public float m_boneWidth = 0.05f;

    public Color m_linkColor = Color.grey;
    public float m_linkWidth = 5;

    public Color m_selBoneColor = Color.red;
    public Transform m_selBone = null;

    public Color m_selChildBoneColor = Color.cyan;
    public bool m_showSelChildBoneColor = true;

    public int m_boneNums = 0;
    public Transform[] m_allSelBones;

    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {

    }

    private void OnDrawGizmos()
    {
        RefreshData();
        DrawLink(transform);
        DrawBone(transform, false);
    }

    private void RefreshData()
    {
        m_boneNums = transform.GetComponentsInChildren<Transform>().Length;

        if (m_selBone == null)
            m_allSelBones = null;
        else
            m_allSelBones = m_selBone.GetComponentsInChildren<Transform>();
    }

    private void DrawLink(Transform node)
    {
        if (node == null) return;
        for (int i = 0; i < node.childCount; i++)
        {
            Transform child = node.GetChild(i);
            UnityEditor.Handles.color = m_linkColor;
            UnityEditor.Handles.DrawAAPolyLine(m_linkWidth, new Vector3[] { node.position, child.position });
            DrawLink(child);
        }
    }

    private void DrawBone(Transform node, bool isParentSelect)
    {
        #region isSelect
        bool isSelect = false;
        if (isParentSelect)
        {
            isSelect = true;
        }            
        else
        {
            isSelect = m_selBone == node;
        }
        #endregion

        #region Draw Bone
        if (isParentSelect && m_showSelChildBoneColor)
            UnityEditor.Handles.color = m_selChildBoneColor;
        else if (isSelect)
            UnityEditor.Handles.color = m_selBoneColor;
        else
            UnityEditor.Handles.color =  m_boneColor;
        UnityEditor.Handles.CubeHandleCap(0, node.position, Quaternion.identity, 
            m_boneWidth, EventType.Repaint);
        #endregion

        // Draw Child
        for (int i = 0; i < node.childCount; i++)
        {
            Transform child = node.GetChild(i);
            DrawBone(child, isSelect);
        }
    }
}

```


