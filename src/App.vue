<template>
  <div>
    <div style="width: 100vw; max-width: 100vw">
      <div :style="{ display: isPc ? 'flex' : '', margin: '10px' }">
        <pre
          v-html="state.currentStyleCode"
          class="styleEditor"
          ref="styleEditorRef"
        ></pre>
        <div class="heartWrapper">
          <div class="red-envelope">
            <header></header>
            <main style="display: none">
              <h2>祝你新年快乐</h2>
              <p style="opacity: 0.7">年年暴富</p>
              <button class="open" @click="open">开</button>
            </main>
          </div>
        </div>
      </div>
    </div>
    <div v-if="state.showPopup" class="popup">
      <p>{{ state.title }}</p>
    </div>
  </div>
</template>

<script setup>
import { reactive, ref, onMounted, onBeforeUnmount, nextTick } from "vue";
// 判断是否是pc
const isPc = ref(true);

const state = reactive({
  fullStyle: [],
  currentStyleCode: "",
  interval: 30, // 控制文字显示时间
  // 展示弹窗
  flag: false,
  showPopup: false,
  title: "💢这么着急，红包还没画完呢🧧！！！",
});

// 控制进度条是最底部
const styleEditorRef = ref(null);
// 插入到html里的style标签
const vnodeStyle = ref(null);

onMounted(() => {
  nextTick(async () => {
    await isMobile();
    insertStyleTag();
    progressiveShowStyle(0);
  });
});

const open = () => {
  if (state.flag) state.title = "💥当然是空包的啦！💨";
  if (state.showPopup) return;
  state.showPopup = true;
  // 两秒后自动关闭弹窗
  setTimeout(() => {
    state.showPopup = false;
  }, 2000);
};

const progressiveShowStyle = async (n = 0) => {
  const showStyle = async (i) => {
    const style = state.fullStyle[n];
    const char = style[i];
    if (!style || !char) {
      state.flag = true;
      return;
    }
    if (char === "\n" && styleEditorRef.value) {
      styleEditorRef.value.scrollTop = 1000000;
    }
    state.currentStyleCode += char;
    vnodeStyle.value.textContent = "";
    vnodeStyle.value.appendChild(
      document.createTextNode(state.currentStyleCode)
    );

    await new Promise((resolve) => setTimeout(resolve, state.interval));
    showStyle(i + 1);
  };
  showStyle(0);
};

const insertStyleTag = () => {
  // 创建一个 style 标签
  vnodeStyle.value = document.createElement("style");
  vnodeStyle.value.appendChild(document.createTextNode(""));
  // 获取整个body
  const targetElement = document.body;
  // 将 style 标签添加到目标元素中
  targetElement.appendChild(vnodeStyle.value);
};

const isMobile = () => {
  let userAgentInfo = navigator.userAgent;
  let Agents = [
    "Android",
    "iPhone",
    "SymbianOS",
    "Windows Phone",
    "iPad",
    "iPod",
  ];
  for (let v = 0; v < Agents.length; v++) {
    if (userAgentInfo.indexOf(Agents[v]) > 0) {
      isPc.value = false;
      break;
    }
  }
  state.fullStyle = [
    `
/*
* 大家好💥。
* 简单介绍一下我们前端工程师的工作
* 我们的工作就是给空白的网页增加彩色🌈和动画🎬。
* 注意！手机和电脑还是有些不同的。
* 你现在用的是---${isPc.value ? "电脑" : "手机"}
*/

/* 首先给所有元素加上过渡效果 */
* {
  -webkit-transition: all .5s;
  transition: all .5s;
}

/* 白色背景太单调了。来点背景 */
body, html {
  color: #fff;
  background-color: #AA3939;
}

/* 文字靠的太近了 */
.styleEditor {
  overflow: auto;
  ${
    isPc.value
      ? `width: 48vw;
  height: 96vh;`
      : `width: 96vw;
  height: 48vh;`
  }
  border: 1px solid;
  padding: 10px 10px 0px 10px;
  font-size: 14px;
  line-height: 1.5;
}

/* 接下来，我们来用代码画一个红包🧧 */

/* 首先来一个画板 */
.heartWrapper {
  ${
    isPc.value
      ? `width: 48vw;
  height: 96vh;`
      : `width: 96vw;
  height: 48vh;`
  }
  border: 1px solid;
  background-color: white;
  position: relative;
}

/* 开始画红包的主体 */
.red-envelope {
	color: #fff;
	padding: 1em;
	height: 20em;
	margin: 1.5em auto;
	max-width: 16em;
  background: #c40b00;
	overflow: hidden;
	position: relative;
	border-radius: 1em;
}

/* 接着是红包的开口 */
.red-envelope header {
	top: -11.5em;
	left: 0;
	right: 0;
	height: 16em;
	position: absolute;
	border-radius: 100%;
	background: #b00b00;
}

/* 新年祝福也得整上 */
.red-envelope main {
  margin-top: 4.5em;
  display:block !important;
	text-align: center;
}

/* 红包的打开按钮 */
.red-envelope .open {
	outline: 0;
	width: 3em;
	height: 3em;
	color: #fff;
	border: none;
	display: block;
	font-size: 2em;
	cursor: pointer;
	margin: 1em auto;
	background: #ffb03a;
	border-radius: 100%;
	transition:
		background 0.3s,
		transform 0.3s;
}

/* 动画安排上 */
@keyframes scaleAnimation {
	from {
		transform: scale(1);
	}
	to {
		transform: scale(1.2);
	}
}

.open {
	animation: 
    scaleAnimation 0.5s ease-in-out 1s infinite;
}

/*
* Ok，完成🌼
* 提前祝你💌：
* 在新的一年里🧧，迎来好运连连，事业步步高升，家庭幸福美满。新年快快乐！
*/
`,
  ];
};

onBeforeUnmount(() => {
  vnodeStyle.value.textContent = "";
});
</script>

<style scoped>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

.styleEditor {
  backface-visibility: hidden;
  -webkit-backface-visibility: hidden;
}

.popup {
  position: fixed;
  top: 8%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 15px 20px;
  background-color: #fff;
  border: 1px solid #ccc;
  box-shadow: 0px 0px 6px rgba(0, 0, 0, 0.12);
  border-radius: 8px;
  color: #aa3939;
  white-space: nowrap;
}
</style>
