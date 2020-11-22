<template>
  <div class="container">
    <div class="enveloppe" @click="toggleEnveloppe">
      <div class="enveloppe-face front">
        <div class="sender">
          <p>Sent by Anonymous
            Paris, France <span class="emoji">🇫🇷</span>
          </p>
          <img src="@/assets/stamp.png" />
        </div>
        <div class="receiver">
          Philippe Eng <br>
          Austin, Texas
        </div>
      </div>
      <div class="enveloppe-face back">
        <div class="flap-seal">
          <div class="flap-face front"></div>
          <div class="flap-face back"></div>
        </div>

        <div class="letter">
          <div class="upper page">
            <p class="greet">Dear Philippe Eng,</p>
            <p class="text-content">Sed ut perspiciatis unde omnis iste natus error sit voluptatem accusantium doloremque laudantium, totam rem aperiam, eaque ipsa quae ab illo inventore veritatis et quasi architecto beatae vitae dicta sunt explicabo.

              Nemo enim ipsam voluptatem quia voluptas sit aspernatur aut odit aut fugit, sed quia consequuntur magni dolores eos qui ratione voluptatem sequi nesciunt.

              Neque porro quisquam est, qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit, sed quia non numquam eius modi tempora incidunt ut labore et dolore magnam aliquam quaerat voluptatem.

              Ut enim ad minima veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur?
            </p>
          </div>
          <div class="lower">
            <div class="lower-face front"></div>
            <div class="lower-face back page">
              <p class="text-content">Nam libero tempore, cum soluta nobis est eligendi optio cumque nihil impedit quo minus id quod maxime placeat facere possimus, omnis voluptas assumenda est, omnis dolor repellendus.

                At vero eos et accusamus et iusto odio dignissimos ducimus qui blanditiis praesentium voluptatum deleniti atque corrupti quos dolores et quas molestias excepturi sint occaecati cupiditate non provident, similique sunt in culpa qui officia deserunt mollitia animi, id est laborum et dolorum fuga.

                Temporibus autem quibusdam et aut officiis debitis aut rerum necessitatibus saepe eveniet ut et voluptates repudiandae sint et molestiae non recusandae.
              </p>
              <p class="text-content sign">
                Signed by Anonymous 🦉
              </p>
            </div>
          </div>
        </div>

        <div class="flap-sides"></div>
        <div class="flap-bottom"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { TimelineLite, TweenLite, CSSPlugin } from 'gsap/all'

// eslint-disable-next-line no-unused-vars
const plugins = [CSSPlugin]

const zIndex = (selector, idx) => () => {
  TweenLite.set(selector, { zIndex: idx })
}

const DURATION = 0.4

export default {
  data: () => ({
    open: false,
    isAnimating: false,
    timeline: new TimelineLite({ paused: true })
  }),
  mounted () {
    TweenLite.set('.flap-seal', { zIndex: 1 })
    this.timeline
      .to('.enveloppe', DURATION, { rotationY: 180, onReverseComplete: () => { this.isAnimating = false } })
      .to('.flap-seal', DURATION, {
        rotationX: -180,
        onComplete: zIndex('.flap-seal', 0)
      })
      .to('.letter', DURATION, { y: -220, onComplete: zIndex('.letter', 1), onReverseComplete: zIndex('.flap-seal', 1) })
      .to('.letter', DURATION, { left: '-10%', width: '120%', height: '155%' }, '-=0.2')
      .to(
        '.letter .lower',
        DURATION,
        { rotationX: -180, onComplete: () => { this.isAnimating = false }, onReverseComplete: zIndex('.letter', 0) },
        '-=0.15'
      )
  },
  methods: {
    toggleEnveloppe () {
      if (this.isAnimating) return
      this.open = !this.open
      this.isAnimating = true
      if (this.open) this.timeline.play()
      else this.timeline.reverse()
    }
  }
}
</script>

<style lang="scss">
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100%;
  background: darken(whitesmoke, 5%);
  perspective: 500px;
}

$color-inside: tan;
$color-opening-front: wheat;
$color-back-sides: darken(wheat, 10%);
$color-back-center: darken(wheat, 14%);

@mixin absolute-position-parent {
  position: absolute;
  height: 100%;
  width: 100%;
}

.enveloppe {
  position: relative;
  width: 300px;
  height: 150px;
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
  transform-style: preserve-3d;
  box-shadow: 0 0 3px 0 rgba(0, 0, 0, 0.3);
  user-select: none;
  .enveloppe-face {
    perspective: 500px; // FIXME: Nested preserve-3d does not seem to work, reset necessary
    @include absolute-position-parent;
    backface-visibility: hidden;
  }
  .enveloppe-face.front {
    padding: 5px;
    background-color: $color-back-sides;
    font-size: 18px;
    display: flex;
    flex-direction: column;
     &::before {
      content: 'Tap on enveloppe to open';
      font-size: 12px;
      opacity: 0.5;
      position: absolute;
      bottom: 5px;
      left: 5px;
    }
    .sender {
      display: flex;
      justify-content: space-between;
      font-size: 13px;
      line-height: 12px;
      img { width: 40px; height: auto; }
    }
    .receiver {
      display: flex;
      flex: 1;
      justify-content: center;
      align-items: center;
      line-height: 17px;
    }
  }
  .enveloppe-face.back {
    transform: rotateY(180deg);
    background-color: $color-inside;
  }
}

.flap-seal {
  @include absolute-position-parent;
  transform-style: preserve-3d;
  transform-origin: top center;
  .flap-face {
    @include absolute-position-parent;
    clip-path: polygon(50% 75%, 100% 0, 0 0);
    backface-visibility: hidden;
  }
  .flap-face.front {
    background-color: $color-opening-front;
  }
  .flap-face.back {
    transform: rotateY(180deg);
    background-color: $color-inside;
    border-top: 1px solid rgba(0, 0, 0, 0.1);
  }
}

.letter {
  position: absolute;
  top: 5%;
  left: 5%;
  height: 90%;
  width: 90%;
  transform-style: preserve-3d;
  .upper {
    @include absolute-position-parent;
    overflow: hidden;
    background-color: white;
  }
  .lower {
    @include absolute-position-parent;
    transform-style: preserve-3d;
    transform-origin: bottom center;
    .lower-face {
      @include absolute-position-parent;
      overflow: hidden;
      background-color: white;
      backface-visibility: hidden;
    }
    .lower-face.back {
      border-top: 1px solid rgba(0, 0, 0, 0.1);
      transform: rotateX(180deg);
      &::before {
        content: 'Tap on the letter to close';
        opacity: 0.5;
        font-size: 14px;
        position: absolute;
        bottom: 5px;
        left: 15px;
      }
    }
  }
}

.flap-bottom,
.flap-sides {
  @include absolute-position-parent;
}

.flap-sides {
  background-color: $color-back-sides;
  clip-path: polygon(50% 75%, 100% 0, 100% 100%, 0 100%, 0 0);
}

.flap-bottom {
  background-color: $color-back-center;
  clip-path: polygon(50% 50%, 100% 100%, 0 100%);
}

p { margin: 0; padding: 0; white-space: pre-line; }
.greet {
  margin: 5px 0;
  font-size: 14px;
  font-weight: 600;
}
.page {
  padding: 0 15px;
  padding-top: 10px;
}
.text-content {
  font-size: 16px;
  line-height: 12px;
}
.sign {
  line-height: 17px;
  text-align: right;
}
.emoji {
  font-size: 11px;
}
</style>
