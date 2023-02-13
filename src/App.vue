<script setup lang="ts">
import { onMounted, ref } from 'vue'
import Card from './components/card.vue'
import { useGame } from './core/useGame'
import { basicCannon, schoolPride } from './core/utils'

const containerRef = ref<HTMLElement | undefined>()
const clickAudioRef = ref<HTMLAudioElement | undefined>()
const dropAudioRef = ref<HTMLAudioElement | undefined>()
const winAudioRef = ref<HTMLAudioElement | undefined>()
const loseAudioRef = ref<HTMLAudioElement | undefined>()
const welAudioRef = ref<HTMLAudioElement | undefined>()
const curLevel = ref(1)
const showTip = ref(false)
const LevelConfig = [
  { cardNum: 3, layerNum: 1, trap: false },
  { cardNum: 5, layerNum: 2, trap: false },
  { cardNum: 7, layerNum: 3, trap: false },
  { cardNum: 8, layerNum: 5, trap: false },
  { cardNum: 9, layerNum: 7, trap: false },
]

const isWin = ref(false)

const {
  nodes,
  selectedNodes,
  handleSelect,
  handleBack,
  backFlag,
  handleRemove,
  removeFlag,
  removeList,
  handleSelectRemove,
  initData,
} = useGame({
  container: containerRef,
  cardNum: 3,
  layerNum: 1,
  trap: false,
  events: {
    clickCallback: handleClickCard,
    dropCallback: handleDropCard,
    winCallback: handleWin,
    loseCallback: handleLose,
  },
})

function handleClickCard() {
  if (clickAudioRef.value?.paused) {
    clickAudioRef.value.play()
  }
  else if (clickAudioRef.value) {
    clickAudioRef.value.load()
    clickAudioRef.value.play()
  }
}

function handleDropCard() {
  dropAudioRef.value?.play()
}

function handleWin() {
  winAudioRef.value?.play()
  // fireworks()
  if (curLevel.value < LevelConfig.length) {
    basicCannon()
    showTip.value = true
    setTimeout(() => {
      showTip.value = false
    }, 1500)
    setTimeout(() => {
      initData(LevelConfig[curLevel.value])
      curLevel.value++
    }, 2000)
  }
  else {
    isWin.value = true
    schoolPride()
  }
}

function handleLose() {
  loseAudioRef.value?.play()
  setTimeout(() => {
    alert('槽位已满，再接再厉~')
    // window.location.reload()
    nodes.value = []
    removeList.value = []
    selectedNodes.value = []
    welAudioRef.value?.play()
    curLevel.value = 0
    showTip.value = true
    setTimeout(() => {
      showTip.value = false
    }, 1500)
    setTimeout(() => {
      initData(LevelConfig[curLevel.value])
      curLevel.value++
    }, 2000)
  }, 500)
}

onMounted(() => {
  initData()
})
</script>



<template>
  <div flex flex-col w-full h-full>
    <div text-44px text-center w-full color="#000000" fw-600 h-60px flex items-center justify-center mt-10px class="textCss">
      兔了个兔d
    </div>
    <div ref="containerRef" flex-1 flex>
      <div w-full relative flex-1>
        <template v-for="item in nodes" :key="item.id">
          <transition name="slide-fade">
            <Card
              v-if="[0, 1].includes(item.state)"
              :node="item"
              @click-card="handleSelect"
            />
          </transition>
        </template>
      </div>
      <transition name="bounce">
        <div v-if="isWin" color="#000" flex items-center justify-center w-full text-28px fw-bold>
          恭喜！成功加入淼圈 (*^_^*) 
        </div>
      </transition>
      <transition name="bounce">
        <div v-if="showTip" color="#000" flex items-center justify-center w-full text-28px fw-bold>
          第{{ curLevel + 1 }}关
        </div>
      </transition>
    </div>

    <div text-center h-50px flex items-center justify-center>
      <Card
        v-for="item in removeList" :key="item.id" :node="item"
        is-dock
        @click-card="handleSelectRemove"
      />
    </div>
    <div w-full flex items-center justify-center>
      <div border="~ 4px dotted #000" w-295px h-44px flex>
        <template v-for="item in selectedNodes" :key="item.id">
          <transition name="bounce">
            <Card
              v-if="item.state === 2"
              :node="item"
              is-dock
            />
          </transition>
        </template>
      </div>
    </div>
    <div class=".heightTest" w-295px h-10px flex></div>
    <div h-50px flex items-center w-full justify-center>
      <button :disabled="removeFlag" mr-10px @click="handleRemove" class="test1">
        放回前三张
      </button>
      <button :disabled="backFlag" @click="handleBack" class="test1">
        放回最后一张
      </button>
    </div>
    <audio
      ref="clickAudioRef"
      style="display: none;"
      controls
      src="./audio/click.mp3"
    />
    <audio
      ref="dropAudioRef"
      style="display: none;"
      controls
      src="./audio/drop.mp3"
    />
    <audio
      ref="winAudioRef"
      style="display: none;"
      controls
      src="./audio/win.mp3"
    />
    <audio
      ref="loseAudioRef"
      style="display: none;"
      controls
      src="./audio/lose.mp3"
    />
    <audio
      ref="welAudioRef"
      style="display: none;"
      controls
      src="./audio/welcome.mp3"
    />
  </div>
</template>

<style>
body{
  /* background-color: #c3fe8b; */
  /* background-image:"./assets/bg/bg1.png"; */
  /* background-image:"../src/assets/bg/bg1.png"; */
  /* background-image: url("https://img1.baidu.com/it/u=2970092393,1081494374&fm=253&fmt=auto&app=120&f=JPEG?w=1280&h=800"); */
}

button{
      border-radius: 255px 15px 225px 15px/15px 225px 15px 255px;
      /*
      Above is shorthand for:
      border-top-left-radius: 255px 15px;
      border-top-right-radius: 15px 225px;
      border-bottom-right-radius: 225px 15px;
      border-bottom-left-radius:15px 255px;
      */
}
.test1{
  border:solid 5px #41403E;
}
/* 
       &:hover{
         box-shadow:2px 8px 4px -6px hsla(0,0%,0%,.3);
      } 
      &.lined.thick{
         border:solid 7px #41403E;
      }
      &.dotted.thick{
         border:dotted 5px #41403E;
      }
      &.dashed.thick{
        border:dashed 5px #41403E;
      }
       &.lined.thin{
         border:solid 2px #41403E;
      }
      &.dotted.thin{
         border:dotted 2px #41403E;
      }
      &.dashed.thin{
        border:dashed 2px #41403E;
      }
    } */


.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}

.slide-fade-enter-active {
  transition: all 0.2s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.2s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateY(25vh);
  opacity: 0;
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
