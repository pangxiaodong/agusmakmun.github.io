---
layout: post
title:  "align ugui recttransform"
date:   2019-02-13 00:16:59
categories: [unity]
---
- Set pivot for the self point of the alignment. 
- Set anchorMin and anchorMax for the parent point of the alignment.
- Set anchoredPosition for the offset of the alignment.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class AlignUtil : MonoBehaviour
{
    public enum AlignType
    {
        LeftTop = 0,
        LeftBottom,
        RightTop,        
        RightBottom
    }

    public RectTransform target;
    public AlignType selfAlign;
    public AlignType targetAlign;
    private readonly Vector2[] aligns = new Vector2[] {
        new Vector2(0, 1), // left-top
        new Vector2(0, 0), // left-bottom
        new Vector2(1, 1), // right-top
        new Vector2(1, 0)  // right-bottom
    };

    public void SetAlign()
    {
        if (target == null) return;

        RectTransform trans = GetComponent<RectTransform>();

        #region save backup
        Transform backupParent = trans.parent;
        #endregion

        #region set align
        trans.SetParent(target);
        trans.pivot = aligns[(int)selfAlign];
        trans.anchorMin = aligns[(int)targetAlign];
        trans.anchorMax = aligns[(int)targetAlign];
        trans.anchoredPosition = new Vector2(0, 0);
        #endregion

        #region recover form backup
        trans.SetParent(backupParent);
        #endregion        
    }
}
```
```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEditor;

namespace UnityEditor
{
    [CustomEditor(typeof(AlignUtil))]
    public class AlignUtilEditor : Editor
    {
        public override void OnInspectorGUI()
        {
            base.OnInspectorGUI();
            AlignUtil util = target as AlignUtil;
            if (GUILayout.Button("Set Align"))
            {
                util.SetAlign();
            }
        }
    }
}
```



![align util]({{ site.baseurl }}/images/align-ugui-recttransform/align_util.png)