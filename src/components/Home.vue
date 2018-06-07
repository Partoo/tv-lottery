<template>
  <div>
    <div id="content">
      <header></header>
        <div id="main">
          <div class="gallery">
            <div v-for="(pic, index) in pics" class="brand">
              <div class="ribbon">
                  <div>{{++index}}</div>
                </div>
              <img :src="'../../static/'+pic.front" style="z-index:9" class="front" @click.prevent="onPicClick(pic.front, pic.back)">
              <img :src="'../../static/'+pic.back" style="z-index:8" class="back">
            </div>
          </div>
        </div>
        <footer></footer>
    </div>
    <div id="cardWrapper">
      <div id="card">
          <img :src="'../../static/'+currentFront" @click.prevent="onFlip" id="front">
          <img :src="'../../static/'+currentBack" id="back" @click.prevent="onReturn">
      </div>
    </div>
    
  </div>
</template>

<script>
  import _ from 'lodash'
  import {
    Elastic,
    Back,
    TimelineMax,
    TweenMax
  } from 'gsap'
  export default {
    data () {
      return {
        pics: {},
        currentFront: '',
        currentBack: '',
        currentEl: ''
        // selectedPos: {}
      }
    },
    methods: {
      onPicClick (front, back) {
        this.currentFront = front
        this.currentBack = back
        this.currentEl = event.target.parentElement
        // let el = event.target.getBoundingClientRect()
        // this.selectedPos = Object.assign({}, {top: el.top, left: el.left})
        TweenMax.set('#card', {scale: 0.5, autoAlpha: 0, display: 'block', transformStyle: 'preserve-3d', transform: 'matrix(1, 0, 0, 1, 0, 0)'})
        // TweenMax.set('#card', {scale: 0.5, autoAlpha: 0, display: 'block', transformStyle: 'preserve-3d'})
        let tl = new TimelineMax()
        tl
        .to('#content', 0.5, {filter: 'blur(10px)'})
        .fromTo('#card', 0.5, {scale: 1.2, autoAlpha: 0}, {scale: 1, autoAlpha: 1, ease: Back.easeOut}, '-=0.4')
      },
      onFlip () {
        TweenMax.set('#cardWrapper', {perspective: 800})
        TweenMax.set('#back', {rotationY: -180})
        TweenMax.set(['#back', '#front'], {backfaceVisibility: 'hidden'})
        TweenMax.to('#card', 2.8, {rotationY: -180, ease: Elastic.easeOut})
      },
      onReturn () {
        let ribbon = this.currentEl.getElementsByClassName('ribbon')
        let back = this.currentEl.getElementsByClassName('back')
        let front = this.currentEl.getElementsByClassName('front')
        let tl = new TimelineMax()
        tl
        .to('#card', 0.8, {scale: 0.1, autoAlpha: 0, rotationY: '-=180', ease: Back.easeOut})
        .to('#content', 0.5, {filter: 'none'}, '-=0.6')
        .to(front, 0.5, {y: '+=20', autoAlpha: 0, ease: Back.easeOut}, '-=0.5')
        .from(back, 0.5, {y: '-=20', autoAlpha: 0, ease: Back.easeOut}, '-=0.4')
        .to(ribbon, 0.5, {opacity: 0}, '-=0.4')
      }
    },
    mounted () {
      var json = require('../../static/config.json')
      this.pics = Object.assign({}, _.orderBy(json.data, ['id']))
    }
  }
</script>

<style lang="scss">
  @import '../sass/ribbon';
  #main {
    display: flex;
    height: calc(100vh - 20vh);
    overflow: hidden;
  }
  header {
    height: 18vh;
  }
  footer {
    // height: 4vh;
  }
  #front {
    z-index: 2;
  }
  #back {
    z-index: 1;
  }
  .gallery {
    width: 100%;
    padding: 0;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    flex-direction: row;
    // align-content: stretch;
    .brand {
      width: 300px;
      height: 220px;
      display: flex;
      flex-wrap: wrap;
    }
  }
  .gallery img {
    flex: 0 0 auto;
    width: 290px;
    // min-height: calc((100vh - 45vh) / 3);
    height: 200px;
    border-radius: 3px;
    padding: 5px;
    background-color: rgba(255, 255, 255, 0.2);
    box-shadow: 1px 20px 30px 15px rgba(0, 0, 0, 0.2);
    border-radius: 10px;
    border: 1px solid rgba(255, 255, 255, 0.5);
    box-shadow: 0 10px 30px 2px rgba(0,0,0,0.3);
    position: absolute;
  }
  .blur {
    // filter: blur(20px);
  }

  #cover {
    // display:none;
    position: relative;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    // background-color:rgba(0, 0, 0, .2);
    z-index: 999;
  }

  #cardWrapper {
    position: absolute;
    top: calc(100vh / 2 - 255px);
    left: calc(100vw / 2 - 461px);
  }

  #card {
    display: none;
    transform-style: preserve-3d;
    z-index: 99;
    width: 922px;
    height: 622px;

    img {
      position: absolute;
      border-radius: 10px;
      padding: 10px;
      background-color: rgba(255, 255, 255, 0.2);
      border-radius: 10px;
      border: 1px solid rgba(255, 255, 255, 0.5);
      box-shadow: 0 1px 10px 2px rgba(0,0,0,0.3);

      width: 922px;
      height: 622px;
    }
  }

</style>