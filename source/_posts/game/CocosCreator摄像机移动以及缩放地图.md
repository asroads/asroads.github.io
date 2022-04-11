---
title: CocosCreator摄像机移动以及缩放地图
comments: true
categories: game
tags:
  - Cocos
abbrlink: 5f9d2fe9
date: 2022-04-11 20:22:19
---

签名文章写过地图移动[地图缩放拖动组件｜Cocos Creator 3.0](https://blog.asroads.com/post/ce78284b.html)，用的是移动节点，后面有文章指出可以移动摄像机完成这样的效果，下面就简单的说一下如何用相机移动实现地图的移动和双指缩放。
<!--more-->

## 环境：

- Mac 
- Cocos Creator2.4.8

下面是效果：

![1001](CocosCreator摄像机移动以及缩放地图/1001.gif)

web 和 原生均可运行 web 使用滚轮 原生使用双指

## 完整代码：

```typescript
/**
 * Created by jsroads on 2022/4/10  12:33
 * Note:
 */
const {ccclass, property} = cc._decorator;

@ccclass
export class MapTouchScript extends cc.Component {
    @property({tooltip: '双指缩放速率', max: 10, min: 0.001})
    public fingerIncreaseRate: number = 1;
    @property({tooltip: "瓦片地图", type: cc.TiledMap})
    private tileMap: cc.TiledMap = null;
    @property({tooltip: "相机", type: cc.Camera})
    private camera: cc.Camera = null;
    @property({tooltip: "缩放比例测试用，可以设置空", type: cc.Label})
    private descLabel: cc.Label = null;
    @property({tooltip: "滚轮滑动比例", type: cc.Integer})
    private increaseRate: number = 10000;
    @property({tooltip: "初始化默认地图缩放比", type: cc.Float})
    private defaultScale: number = 1;
    @property({tooltip: "初始化默认地图坐标"})
    private defaultPosition: cc.Vec2 = new cc.Vec2(0);
    @property({tooltip: "缩放最小比例", type: cc.Float})
    private minZoomRatio: number = 1;
    @property({tooltip: "缩放最大比例", type: cc.Float})
    private maxZoomRatio: number = 3;
    private begin: cc.Vec3;
    private isMoving: boolean = false; // 是否拖动地图flag
    protected onLoad() {
        cc.macro.ENABLE_TILEDMAP_CULLING = true;
        this.camera.zoomRatio = this.defaultScale;
        this.camera.node.position = cc.v3(this.defaultPosition.x, this.defaultPosition.y, 0);
        // 开始触摸
        this.tileMap.node.on(cc.Node.EventType.TOUCH_START, (event: cc.Event.EventTouch)=>{
            this.begin = this.camera.node.position;
            cc.log("begin: " + this.begin);
        }, this);

        // 触摸结束
        this.tileMap.node.on(cc.Node.EventType.TOUCH_END, this.releaseTouchesEnd, this);
        this.tileMap.node.on(cc.Node.EventType.TOUCH_CANCEL, this.releaseTouchesEnd, this);
        // 触摸中
        this.tileMap.node.on(cc.Node.EventType.TOUCH_MOVE, this.onTouchMove, this);
        if (cc.sys.browserType === "chrome" || cc.sys.browserType === "unknown") {
            this.tileMap.node.on(cc.Node.EventType.MOUSE_WHEEL, (event: cc.Event.EventMouse)=>{
                //滑轮缩放大小
                let scale: number = this.camera.zoomRatio - event.getScrollY() / this.increaseRate * -1;
                let screenPos: cc.Vec2 = event.getLocation();
                // 转化
                let realPos = cc.v3(0, 0, 0);
                this.camera.getScreenToWorldPoint(screenPos, realPos);
                let targetPos = this.camera.node.parent.convertToNodeSpaceAR(cc.v3(realPos.x, realPos.y, 0));
                console.log("smile----realPos:" + JSON.stringify(realPos));
                console.log("smile----targetPos:" + JSON.stringify(targetPos));
                // this.targetScale += 0.5
                // let targetScale: number = scale;
                this.smoothOperate(this.camera, targetPos, scale);
                event.stopPropagation();
            }, this);
        }

    }

    private releaseTouchesEnd(event: cc.Event.EventTouch) {
        let touches: any[] = event.getTouches();
        if (touches.length <= 1) {
            this.isMoving = false; // 当容器中仅剩最后一个触摸点时将移动flag还原
        }
        event.stopPropagation();
    }

    private onTouchMove(event: cc.Event.EventTouch) {
        let touches = event.getTouches();
        if (touches.length === 1) {
            const touch: cc.Touch = touches[0];
            if (this.isMoving || touch.getDelta().mag() > 10) {
                this.isMoving = true;
                this.begin = this.begin.subtract(cc.v3(event.getDeltaX(), event.getDeltaY(), 0).divide(this.camera.zoomRatio));
                // console.log("smile----this.begin:" + JSON.stringify(this.begin));
                this.begin = this.dealScalePos(this.begin, this.camera.zoomRatio);
                this.camera.node.setPosition(this.begin);
            } else {
                console.log("距离小，不能移动");
            }

        } else if (touches.length === 2) {
            // multi touch
            this.isMoving = true;
            let touch1: cc.Touch = touches[0];
            let touch2: cc.Touch = touches[1];
            let delta1: cc.Vec2 = cc.v2(touch1.getDelta());
            let delta2: cc.Vec2 = cc.v2(touch2.getDelta());
            let touchPoint1: cc.Vec3 = this.camera.node.convertToNodeSpaceAR(cc.v3(touch1.getLocation().x, touch1.getLocation().y));
            let touchPoint2: cc.Vec3 = this.camera.node.convertToNodeSpaceAR(cc.v3(touch2.getLocation().x, touch2.getLocation().y));
            let distance: cc.Vec3 = touchPoint1.clone().subtract(touchPoint2.clone());
            let delta: cc.Vec2 = delta1.subtract(delta2).multiplyScalar(this.fingerIncreaseRate).divide(this.camera.zoomRatio);
            let targetScale: number;
            if (Math.abs(distance.x) > Math.abs(distance.y)) {
                targetScale = (distance.x + delta.x) / distance.x * this.camera.zoomRatio;
            } else {
                targetScale = (distance.y + delta.y) / distance.y * this.camera.zoomRatio;
            }
            // 转化
            let centerX = (touch1.getLocation().x + touch2.getLocation().x) / 2;
            let centerY = (touch1.getLocation().y + touch2.getLocation().y) / 2;
            let screenPos: cc.Vec3 = cc.v3(centerX, centerY, 0);
            let realPos = cc.v3(0, 0, 0);
            this.camera.getScreenToWorldPoint(screenPos, realPos);
            let targetPos = this.camera.node.parent.convertToNodeSpaceAR(cc.v3(realPos.x, realPos.y, 0));
            console.log("Move centerPos:" + JSON.stringify(screenPos));
            console.log("Move realPos:" + JSON.stringify(realPos));
            console.log("Move targetPos:" + JSON.stringify(targetPos));
            this.smoothOperate(this.camera, targetPos, targetScale);
        }
        event.stopPropagation();
    }

    private smoothOperate(camera: cc.Camera, targetPos: cc.Vec3, targetScale: number): void {
        if (targetScale > this.maxZoomRatio || targetScale < this.minZoomRatio) {
            return;
        }
        targetScale = cc.misc.clampf(targetScale, this.minZoomRatio, this.maxZoomRatio);
        let uiTouchPos: cc.Vec3 = (targetPos.clone().subtract(camera.node.position.clone())).multiplyScalar(camera.zoomRatio);
        let mapPos: cc.Vec3 = targetPos.clone().subtract(uiTouchPos.divide(targetScale));
        console.log("mapPos:" + JSON.stringify(mapPos));
        mapPos = this.dealScalePos(mapPos, targetScale);
        camera.zoomRatio = targetScale;
        camera.node.position = mapPos;
        // render ui
        if (cc.isValid(this.descLabel))
            this.descLabel.string = `${Math.floor(targetScale * 100)}%`;
    }

    private dealScalePos(targetPos: cc.Vec3, zoomRatio: number): cc.Vec3 {
        // 摄像机边界 约束
        const tileMapNode = this.tileMap.node, winSize = cc.winSize;
        let maxX = ((tileMapNode.width * tileMapNode.scaleX - winSize.width / zoomRatio) / 2);
        let maxY = ((tileMapNode.height * tileMapNode.scaleY - winSize.height / zoomRatio) / 2);
        targetPos.x = cc.misc.clampf(targetPos.x, -maxX, maxX);
        targetPos.y = cc.misc.clampf(targetPos.y, -maxY, maxY);
        return targetPos;
    }
}

```

## 相关资源

地图资源下载：https://opengameart.org/content/terrain-transitions

地图编辑器下载：https://github.com/mapeditor/tiled

特定版本1.2：https://github.com/mapeditor/tiled/releases/tag/v1.2.0

## 参考

- [TiledMap(瓦片地图)组件](https://www.writebug.com/git/lili/cocos_creator3-TiledMa)
- [CocosCreator摄像机缩放移动后坐标转化](https://blog.csdn.net/kuokuo666/article/details/104131738)