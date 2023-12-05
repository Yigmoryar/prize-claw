<template>
    <div class="page-portrait" id="page-portrait">
        <div id="pageContainer" class="page-container game-box">
            <div class="poster-main">
                <ul class="poster-list">
                    <template v-for="(wawa, i) in wawaList">
                        <li class="item ani-wawa-y" :class="['lw' + (i + 1), i == wawaIndex ? 'begot' : '']">
                            <img :src="'/images/dx-lw' + (i + 1) + '.png'" alt="" />
                        </li>
                    </template>
                    <!-- <li class="item lw1 ani-wawa-y"><img src="/images/dx-lw1.png" alt=""></li>
                    <li class="item lw2 ani-wawa-y"><img src="/images/dx-lw2.png" alt=""></li>
                    <li class="item lw3 ani-wawa-y"><img src="/images/dx-lw3.png" alt=""></li>
                    <li class="item lw4 ani-wawa-y"><img src="/images/dx-lw4.png" alt=""></li>
                    <li class="item lw5 ani-wawa-y"><img src="/images/dx-lw5.png" alt=""></li>
                    <li class="item lw6 ani-wawa-y"><img src="/images/dx-lw6.png" alt=""></li> -->
                </ul>
            </div>
            <div id="left" class="btn btn-left" @click="handleLeftClick"></div>
            <div id="go" class="btn btn-go" @click="handleGoClick"></div>
            <div id="right" class="btn btn-right" @click="handleRightClick"></div>
            <div class="zhua-top ani-zhua-x" :style="{ left: jiaziStoppedX + 'px' }">
                <div class="zhua-zuo"></div>
                <div class="zhua-gan ani-zhua-gan-y" :style="{ height: ganStoppedHeight + 'px' }"></div>
                <div class="zhua-jiazi ani-zhua-jiazi-y" :class="{ gotit: jiaziHasWawa, 'ani-kaihe': !jiaziHasWawa }"
                    :style="{ top: jiaziStoppedY + 'px' }"></div>
            </div>
        </div>
    </div>
</template>

<script setup>
/**
 * 1、点击左右方向按钮，可以调节抓手的位置
 * 2、点击GO按钮，竿子开始向娃娃位置竖直移动，同时抓手仍然运行着开合动画并竖直移动
 * 3、点击GO按钮的时候，进行一次随机判断，确定是抓手移动至前排娃娃或后排娃娃
 * 4、进行了2S延迟（配置的transition是2s）后，比对竿子的位置，是否与前排或后排中的娃娃相接近，在5px范围内表示成功
 * 5、如果抓取成功，娃娃竖直向上移动，竿子竖直向上移动，抓手不再开合以中等宽度竖直移动
 * 6、弹出提示信息，然后娃娃回到原来的位置，抓手重新进行开合动画
 */

import { ref } from 'vue';

/**
 * 确定用来逻辑判断和计算的数据有哪些？
 * 1、娃娃的位置，用于判断是否成功抓取
 * 2、抓手初始的位置
 * 3、抓手左右移动后停下的位置，用于判断是否成功抓取
 * 4、抓手停的位置，用于判断抓取的娃娃是前排还是后排
 * 5、抓手是否处于有娃娃被抓住的状态，用于判断是否需要运行抓手开合的动画
 * 6、
 */

// x: 用于判断抓手左右方向的位置，y: 用于判断抓手竖直方向的位置，top: 娃娃被抓住后上移后停住的位置, isFront: 用于判断是否前排
const wawaList = [
    { x: -134, y: 317, top: -199, isFront: false }, { x: -6, y: 317, top: -199, isFront: false }, { x: 119, y: 317, top: -199, isFront: false },
    { x: -107, y: 387, top: -199, isFront: true }, { x: 21, y: 387, top: -199, isFront: true }, { x: 151, y: 387, top: -199, isFront: true },
];
const jiaziInitedX = 0; // 抓手整体的left属性的值
const jiaziInitedY = 117; // 抓手top属性的初始值
const ganInitedHeight = 100; // 竿子height属性的初始值
const frontHeight = 370; // 竿子在前排时停下的位置 height属性的值
const backHeight = 300; //  竿子在后排时停下的位置 height属性的值
const frontY = 387;  // 抓手在前排时下降停下的位置 top属性的值
const backY = 317;   // 抓手在后排时下降停下的位置 top属性的值

const wawaIndex = ref(-1); // 被抓住的娃娃序号
const jiaziStoppedX = ref(0); // 左右移动后，最终停下的位置
const jiaziStoppedY = ref(117);  //  竖直方向上最终停下的位置
const ganStoppedHeight = ref(100);   // 竿子最终的高度
const jiaziHasWawa = ref(false);  // 抓手是否夹住了娃娃

const handleLeftClick = () => {
    // 点击 左 按钮时
    jiaziStoppedX.value -= 5;
}

const handleRightClick = () => {
    // 点击 右 按钮时
    jiaziStoppedX.value += 5;
}

const handleGoClick = () => {
    if(wawaIndex.value > -1){
        wawaIndex.value = -1;
        jiaziHasWawa.value = false;
        return;
    }

    // 产生一个随机数，用于决定抓手移至前排或后排
    const randomNum = Math.random();
    let isFront = false;
    if (randomNum < 0.5) {
        isFront = true;
    } else {
        isFront = false;
    }

    // 点击 Go 按钮时, 根据前面决定的前排或后排，更新抓手和竿子的top或height属性
    if (isFront) {
        ganStoppedHeight.value = frontHeight;
        jiaziStoppedY.value = frontY;
    } else {
        ganStoppedHeight.value = backHeight;
        jiaziStoppedY.value = backY;
    }

    // 计算有没有成功抓着娃娃
    // 采用循环判断娃娃，看夹子的水平和竖直方向的值是否与娃娃的位置相匹配
    let hasGotWawa = false;
    let begotIndex = -1;
    for (let i = 0; i < wawaList.length; i++) {
        const v = wawaList[i];
        const isXMatch = Math.abs(v.x - jiaziStoppedX.value) < 5;
        console.log(v.x, jiaziStoppedX.value);
        const isYMatch = isFront ? (jiaziStoppedY.value == frontY) : (jiaziStoppedY.value == backY);
        const isFrontMatch = isFront == v.isFront;
        if (isXMatch && isYMatch && isFrontMatch) {
            hasGotWawa = true;
            begotIndex = i;
            break;
        } else {
            hasGotWawa = false;
        }
    }
    console.log('isFront:', isFront, hasGotWawa, begotIndex);

    // 上述代码执行后，抓手会向下移动，动画会持续2秒，
    // 下面的代码会在2秒后执行
    setTimeout(() => {
        // 这时候，抓手会在最低的位置停留800毫秒，我们把抓手的开合动画关掉

        jiaziHasWawa.value = false;

        setTimeout(() => {
            // 在800毫秒后，
            // 如果有抓到娃娃，抓手的开合动画会停止
            // 如果没有抓到，抓手的开合动画也会停止
            jiaziHasWawa.value = hasGotWawa;

            ganStoppedHeight.value = ganInitedHeight;
            jiaziStoppedY.value = jiaziInitedY;
            wawaIndex.value = begotIndex;

            setTimeout(() => {

            }, 2000);
        }, 800);
    }, 2000)
}

</script>

<style scoped>
.page-portrait {
    @apply h-full mx-auto;
}

.page-container {
    @apply h-full w-[640px] bg-[url('/images/dx-bg2.jpg')] bg-no-repeat mx-auto relative overflow-hidden;
}

.poster-main {
    @apply absolute left-0 top-[520px] h-[120px] w-full max-w-full;

    /** 这里定义的是娃娃机中的娃娃位置
     *  可以分成两排摆放，两排中的娃娃可以不用对齐
     *  但需要记住两排娃娃的上下及左右相对位置，用于判断是否能成功抓到
     */
    .poster-list {
        @apply absolute left-0 top-0 h-full w-full;

        .item {
            position: absolute;
            top: 0;
            height: 120px;
            widows: 125px;
        }

        .begot {
            top: -199px;
        }

        .lw1 {
            left: 128px;
        }

        .lw2 {
            left: 253px;
        }

        .lw3 {
            left: 378px;
        }

        .lw4 {
            left: 162px;
            top: 68px;

        }

        .lw5 {
            left: 290px;
            top: 68px;
        }

        .lw6 {
            left: 420px;
            top: 68px;
        }
    }
}

/** 这里定义的是三个按钮的样式
 *  
 */
.btn {
    @apply absolute top-[706px] h-[120px] w-[125px] border;

    &.btn-left {
        @apply left-[80px];
    }

    &.btn-go {
        @apply left-[260px];
    }

    &.btn-right {
        @apply left-[437px];
    }
}

/** 这里定义的是抓手的位置样式 
 *
 */
.zhua-top {
    @apply absolute h-[490px] w-full top-[118px] left-0 mx-auto;

    .zhua-zuo {
        @apply absolute w-[58px] h-[17px] top-0 left-1/2 ml-[-29px] bg-[url('/images/dx-zuo.png')];
    }

    .zhua-gan {
        @apply absolute w-[22px] h-[100px] top-[17px] left-1/2 ml-[-11px] bg-[url('/images/dx-gan.png')]
    }

    .zhua-jiazi {
        @apply absolute w-[135px] h-[123px] top-[117px] left-1/2 ml-[-67.5px] bg-[url('/images/dx-zhua1.png')] bg-center bg-no-repeat;
    }

}

.gotit {
    /* 使用中等宽度的夹子，表示刚好夹住娃娃 */
    @apply bg-[url('/images/dx-zhua2.png')] !important;
}

/** 这里定义使用动画的CSS规则
 *  使用关键帧实现
 *      抓手的开合:
 *          animation: kaihe
 *  使用transition定义渐变的属性以及时间，可以实现平滑的更改属性值，达到动画的效果
 *      竿子的下降、上升 ani-zhua-gan-y：
 *          transition: height 2s;
 *      抓手的下降、上升 ani-zhua-jiazi-y：
 *          transition: top 2s;
 *      娃娃的下降、上升 ani-wawa-y：
 *          transition: top 2s; 
 *      抓手及竿子的左右移动 ani-zhua-x：
 *          transition: left 0.3s;             
 */
.ani-kaihe {
    animation: kaihe 0.6s steps(1) infinite;
}

.ani-zhua-x {
    transition: left 0.3s;
}

.ani-zhua-gan-y {
    transition: height 2s;
}

.ani-zhua-jiazi-y {
    transition: top 2s;
}

.ani-wawa-y {
    transition: top 2s;
}

/** 这里定义的是需要用到的关键帧动画
 *  kaihe:  用于抓手的开合
 */
@keyframes kaihe {
    0% {
        background-image: url(/images/dx-zhua3.png);
    }

    25% {
        background-image: url(/images/dx-zhua2.png);
    }

    50% {
        background-image: url(/images/dx-zhua1.png);
    }

    75% {
        background-image: url(/images/dx-zhua2.png);
    }

    100% {
        background-image: url(/images/dx-zhua3.png);
    }
}
</style>