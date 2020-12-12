<template>
  <div class="pulldown-loading-wrapper">
    <div class="wrapper">
      <span class="doc doc-1"></span>
      <span class="doc doc-2"></span>
      <span class="doc doc-3"></span>
      <span class="doc doc-4"></span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PulldownLoading'
}
</script>

<style lang="scss" scoped>
$bg1: #0098ff;
$bg2: #21b30c;
$bg3: #f40;
$bg4: rgb(226, 248, 23);

.pulldown-loading-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
  font-size: 12px;

  .wrapper {
    position: relative;
    width: 3em;
    height: 3em;
    transform-origin: center;
    transform: rotate(0);
    animation: wrapperAnimation 1s linear infinite;

    .doc {
      display: block;
      position: absolute;
      width: 1em;
      height: 1em;
      border-radius: .5em;
      
      &.doc-1 {
        left: 0;
        top: 0;
        background: $bg1;
        animation: doc1Animation .5s linear infinite alternate;
      }

      &.doc-2 {
        left: 0;
        bottom: 0;
        background: $bg2;
        animation: doc2Animation .5s linear infinite alternate;
      }

      &.doc-3 {
        right: 0;
        bottom: 0;
        background: $bg3;
        animation: doc3Animation .5s linear infinite alternate;
      }

      &.doc-4 {
        right: 0;
        top: 0;
        background: $bg4;
        animation: doc4Animation .5s linear infinite alternate;
      }
    }
  }
}

@keyframes wrapperAnimation {
  from {
    transform: rotate(0);
  }

  to {
    transform: rotate(1turn);
  }
}

@keyframes doc1Animation {
  from {
    left: 0;
    top: 0;
  }

  to {
    left: .5em;
    top: .5em;
  }
}

@keyframes doc2Animation {
  from {
    left: 0;
    bottom: 0;
  }

  to {
    left: .5em;
    bottom: .5em;
  }
}

@keyframes doc3Animation {
  from {
    right: 0;
    bottom: 0;
  }

  to {
    right: .5em;
    bottom: .5em;
  }
}

@keyframes doc4Animation {
  from {
    right: 0;
    top: 0;
  }

  to {
    right: .5em;
    top: .5em;
  }
}
</style>
