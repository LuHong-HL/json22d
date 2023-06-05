<template>
  <div id="app">
    <div ref="container"></div>
  </div>
</template>

<script>
import * as PIXI from "pixi.js";

export default {
  name: "App",
  components: {},
  data() {
    return {
      shapes: [
        { id: "shape1", x: 50, y: 50, width: 100, height: 100 },
        { id: "shape2", x: 200, y: 50, width: 100, height: 100 },
        { id: "shape3", x: 50, y: 200, width: 100, height: 100 },
        { id: "shape5", x: 200, y: 200, width: 120, height: 140 },
        { id: "shape4", x: 200, y: 200, width: 100, height: 100 },
      ],
    };
  },
  mounted() {
    // 创建一个PixiJS应用程序
    const app = new PIXI.Application({
      backgroundColor: 0xffffff,
      resizeTo: window,
    });

    // 将PixiJS应用程序添加到DOM中
    this.$refs.container.appendChild(app.view);

    // create a texture from an image path
    const texture = PIXI.Texture.from("textures/sun.png");

    // Scale mode for pixelation
    texture.baseTexture.scaleMode = PIXI.SCALE_MODES.NEAREST;

    for (let i = 0; i < 10; i++) {
      createBunny(
        Math.floor(Math.random() * app.screen.width),
        Math.floor(Math.random() * app.screen.height)
      );
      createRect(
        Math.floor(Math.random() * app.screen.width),
        Math.floor(Math.random() * app.screen.height)
      );
    }
    createGraphics();

    function createRect(x, y) {
      // create our little bunny friend..
      const rect = new PIXI.Graphics();

      rect.beginFill(0xff0000);
      rect.drawRect(0, 0, 100, 100);
      rect.endFill();
      // enable the bunny to be interactive... this will allow it to respond to mouse and touch events
      rect.interactive = true;

      // this button mode will mean the hand cursor appears when you roll over the bunny with your mouse
      rect.cursor = "pointer";

      // center the bunny's anchor point
      // rect.anchor.set(0.5);

      // make it a bit bigger, so it's easier to grab
      // rect.scale.set(0.1);

      rect.alpha = 0.3;

      // setup events for mouse + touch using
      // the pointer events
      rect.on("pointerdown", onDragStart, rect);

      // move the sprite to its designated position
      rect.x = x;
      rect.y = y;

      // add it to the stage
      app.stage.addChild(rect);
    }

    function createBunny(x, y) {
      // create our little bunny friend..
      const bunny = new PIXI.Sprite(texture);

      // enable the bunny to be interactive... this will allow it to respond to mouse and touch events
      bunny.interactive = true;

      // this button mode will mean the hand cursor appears when you roll over the bunny with your mouse
      bunny.cursor = "pointer";

      // center the bunny's anchor point
      bunny.anchor.set(0.5);

      // make it a bit bigger, so it's easier to grab
      bunny.scale.set(0.1);

      // setup events for mouse + touch using
      // the pointer events
      bunny.on("pointerdown", onDragStart, bunny);
      // 添加点击事件
      bunny.on("click", () => {
        console.log("on click bunny", x, y);
      });

      // move the sprite to its designated position
      bunny.x = x;
      bunny.y = y;

      // add it to the stage
      app.stage.addChild(bunny);
    }

    // 画不规则的图形，包括弧形
    function createGraphics() {
      let id = "graphics";
      const graphics = new PIXI.Graphics();

      // 绘制不规则图形
      graphics.beginFill(0xffffff, 0.01);
      graphics.lineStyle(2, 0x000000, 1);
      graphics.moveTo(0, 0);
      graphics.lineTo(100, 0);
      graphics.lineTo(100, 50);
      graphics.arc(50, 50, 50, 0, Math.PI / 2, false);
      graphics.lineTo(0, 50);
      graphics.closePath();
      graphics.endFill();

      graphics.interactive = true;

      // this button mode will mean the hand cursor appears when you roll over the bunny with your mouse
      graphics.cursor = "pointer";

      graphics.on("pointerdown", onDragStart, graphics);

      // 添加点击事件
      graphics.on("click", () => {
        console.log("on click", id);
      });

      // 将图形添加到舞台
      app.stage.addChild(graphics);
    }

    let dragTarget = null;
    let oldAlpha = 1;

    app.stage.interactive = true;
    app.stage.hitArea = app.screen;
    app.stage.on("pointerup", onDragEnd);
    app.stage.on("pointerupoutside", onDragEnd);

    function onDragMove(event) {
      if (dragTarget) {
        dragTarget.parent.toLocal(event.global, null, dragTarget.position);
      }
    }

    function onDragStart() {
      // store a reference to the data
      // the reason for this is because of multitouch
      // we want to track the movement of this particular touch
      // this.data = event.data;
      oldAlpha = this.alpha;
      this.alpha = oldAlpha - 0.5 > 0.1 ? oldAlpha - 0.5 : 0.1;
      dragTarget = this;
      app.stage.on("pointermove", onDragMove);
    }

    function onDragEnd() {
      if (dragTarget) {
        app.stage.off("pointermove", onDragMove);
        dragTarget.alpha = oldAlpha;
        dragTarget = null;
      }
    }
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
* {
  margin: 0;
  padding: 0;
}
</style>
