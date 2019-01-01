---
layout: post
title:  "simple camera script"
date:   2019-01-01 00:18:23 +0700
categories: [unity]
---
[Youtube: Unity 5 Third Person Camera](https://www.youtube.com/watch?v=Ta7v27yySKs)

[脚本下载](https://n3k.ca/downloads/bin/sc-3rd-camera.unitypackage)

[作者站点](https://n3k.ca/#Download)

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraControl : MonoBehaviour
{
    private const float Y_ANGLE_MIN = 0.0f; 
    private const float Y_ANGLE_MAX = 50.0f;

    public Transform lookAt;
    public Transform camTransform;
    public float distance = 10.0f;

    private float currentX = 0.0f;
    private float currentY = 45.0f;
    private float sensitivityX = 4.0f;
    private float sensitivityY = 1.0f;

    private void Start()
    {
        camTransform = transform;
    }

    private void Update()
    {
        currentX += Input.GetAxis("Mouse X");
        currentY += Input.GetAxis("Mouse Y");

        currentY = Mathf.Clamp(currentY, Y_ANGLE_MIN, Y_ANGLE_MAX);
    }

    private void LateUpdate()
    {
        Vector3 dir = new Vector3(0, 0, -distance);
        Quaternion rotation = Quaternion.Euler(currentY, currentX, 0); 
        camTransform.position = lookAt.position + rotation * dir;
        camTransform.LookAt(lookAt.position);
    }
}
```