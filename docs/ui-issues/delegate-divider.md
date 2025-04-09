# Delegate分割线粗细不均匀的问题

在开发过程中，使用一个ListView创建若干Delegate，每个Delegate内部包含一个高度为1的分割线。

代码如下：

```qml
ListView {
    id: listView
    anchors.fill: parent
    model: 20
    
    delegate: Column {
        width: listView.width
        
        Rectangle {
            width: parent.width
            height: 40
            color: "#f0f0f0"
            
            Text {
                anchors.centerIn: parent
                text: "row " + (index + 1)
            }
        }
        
        Rectangle {
            width: parent.width
            height: 1
            color: "#cccccc"
            visible: index < listView.count - 1
        }
    }
}
```

发现尽管指定的分割线高度均为1，但是显示的粗细不均匀，如下图：

![分割线粗细不均匀](./images/delegate-divider-uneven.png)

## 原因

原因是Windows的显示设置中设置了缩放为125%，这导致实际上的分割线高度为1.25。

而像素的显示必须是整数，剩余的高度会被累计，不到2时均显示为1，而间隔4个delegate时，多余的高度累计超过1，于是最后一个delegate的高度显示为2。

## 解决方法

为了兼容用户对缩放率的设置，我们delegate这类重复对象的高度时，应该使用`Screen.devicePixelRatio`来保证实际的物理像素为1。

```
height: 1 / Screen.devicePixelRatio
```

![分割线粗细均匀](./images/delegate-divider-fixed.png)
