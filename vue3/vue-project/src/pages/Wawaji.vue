<template>
    <div class="page-portrait" id="page-portrait">
        <div id="pageContainer" class="page-container game-box">
            <div class="poster-main">
                <ul class="poster-list">
                    <li class="item lw1"><img src="/images/dx-lw1.png" alt=""></li>
                    <li class="item lw2"><img src="/images/dx-lw2.png" alt=""></li>
                    <li class="item lw3"><img src="/images/dx-lw3.png" alt=""></li>
                    <li class="item lw4"><img src="/images/dx-lw4.png" alt=""></li>
                    <li class="item lw5"><img src="/images/dx-lw5.png" alt=""></li>
                    <li class="item lw6"><img src="/images/dx-lw6.png" alt=""></li>
                </ul>
            </div>
            <div id="left" class="left-btn"></div>
            <div id="stop" class="button"></div>
            <div id="right" class="right-btn"></div>
            <div class="zhua-top">
                <span class="zhua-zhu"></span>
                <span class="zhua-zuo"></span>
                <div class="zhua zhuamove"></div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.page-portrait {
    @apply h-full mx-auto;
}

.page-container {
    @apply h-full w-[640px] bg-[url('/images/dx-bg2.jpg')] bg-no-repeat mx-auto relative;
}

.poster-main {
    @apply absolute left-0 top-[560px] h-[120px] w-full max-w-full;

    .poster-list {
        @apply absolute left-0 top-0 h-full w-full;

        .item {
            position: absolute;
            top: 0;
            height: 120px;
            widows: 125px;
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
            left: 128px;
            top: 20px;

        }

        .lw5 {
            left: 253px;
            top: 20px;
        }

        .lw6 {
            left: 378px;
            top: 20px;
        }
    }
}

.zhua-top {
    @apply absolute h-[490px] w-full top-[118px] left-0 mx-auto;

    .zhua-zuo {
        @apply absolute w-[58px] h-[17px] top-0 left-1/2 ml-[-29px] bg-[url('/images/dx-zuo.png')];

    }

    .zhua-zhu {
        @apply absolute w-[22px] h-[140px] top-0 left-1/2 ml-[-11px] bg-[url('/images/dx-gan.png')]
    }

    .zhuamove {
        @apply absolute w-[135px] h-[123px] top-[138px] left-1/2 ml-[-67.5px] bg-[url('/images/dx-zhua1.png')] bg-center bg-no-repeat;
        animation: run 0.6s steps(1) infinite;
    }
}

@keyframes run {
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