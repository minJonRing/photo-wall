<template>
  <div id="app">
    <div class="photo-wall">

    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      param: {
        // 行
        row: 10,
        // 列  
        column: 20,

        startXPos: 60,
        startZPos: -100,

        distanceRandomMinX: 55,
        distanceRandomMaxX: 65,

        distanceRandomMinY: 60,
        distanceRandomMaxY: 60,

        initMoveDistance: 1200,

        enlargeSize: 5,
        radiateSize: 220,
      },
      goList: [],
      itemPosDict: [],
      changedItemList: [],
      // 
    }
  },
  methods: {
    Start() {
      // this.CreateGos();
    },
    CreateGos() {
      // 生成所有物体，并添加到字典
      // const { row, column } = this.param
      // for (let i = 0; i < row; i++) {
      //   const gos = [];
      //   this.goList.push(gos);
      //   let lastPosX = 0;
      //   for (let j = 0; j < column; j++) {
      //     const item = {};
      //     item.name = i + "" + j;
      //     const startPos = {
      //       x: this.randomRange(55, 60) + lastPosX,
      //       y: startZPos - i * this.randomRange(60, 60)
      //     }
      //     item.anchoredPosition = startPos;
      //     // const endPos = new Vector3(startPos.x - initMoveDistance, startZPos - i * Random.Range(distanceRandomMinY, distanceRandomMaxY));
      //     const endPos = { x: startPos.x - initMoveDistance, y: startZPos - i * (Math.floor(Math.random() * distanceRandomMaxY) + distanceRandomMinY) };
      //     const tweener = item.DOAnchorPosX(endPos.x, Random.Range(1.8f, 2f));  // 缓动到目标位置
      //     tweener.SetDelay(j * 0.1f + (row - i) * 0.1f);      // 延时
      //     tweener.SetEase(Ease.InSine);           // 缓动效果
      //     item.gameObject.SetActive(true);
      //     gos.push(item);
      //     itemPosDict.push(item, endPos);
      //     lastPosX = item.anchoredPosition.x;
      //   }
      // }
    },
    randomRange(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min
    }
  }
}
</script>

<style>
html,
body {
  height: 100%;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  height: 100%;
}
</style>
