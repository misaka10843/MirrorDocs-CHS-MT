---
description: 有ID吗？
---

# IDs (标识符)

## 资源ID <a href="#asset-id" id="asset-id"></a>

Mirror 使用 GUID 作为资源ID。每个带有 NetworkIdentity 组件的预制体都有一个资源ID，它只是 Unity 的 AssetDatabase.AssetPathToGUID 转换为 16 字节。Mirror 需要知道这个以便知道要生成哪些预制体。

## 场景ID <a href="#scene-id" id="scene-id"></a>

Mirror 使用 uint 作为场景ID。场景（层次结构）中每个带有 NetworkIdentity 的游戏对象在 OnPostProcessScene 中被分配一个场景ID。Mirror 需要知道这个以便区分场景中的不同对象，因为 Unity 没有为场景中不同游戏对象提供唯一ID。

## 网络实例ID（又名 NetId）<a href="#network-instance-id-aka-netid" id="network-instance-id-aka-netid"></a>

Mirror 使用 uint 作为 NetId。每个 NetworkIdentity 在 NetworkIdentity.OnStartServer 中或生成后被分配一个 NetId。Mirror 在客户端和服务器之间传递消息时使用这个ID来告诉消息的接收者是哪个对象。

## 连接ID <a href="#connection-id" id="connection-id"></a>

每个网络连接都有一个连接ID，由底层传输层分配。连接ID 0 保留给服务器也是客户端（主机）时的本地连接。
