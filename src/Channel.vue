<template lang="html">
  <div class="column is-narrow">
    <div class="box is-6">
      <div class="level" style="margin-top:0;margin-bottom:0">
        <!-- lazy input change for buefy component https://github.com/buefy/buefy/issues/401 -->
        <div class="title is-6">Channel</div>
        <div class="level-right">
          <b-tooltip
            label="Play/Pause"
            :delay="1000"
            type="is-link"
            position="is-bottom"
          >
            <b-button
              rounded
              size="is-small"
              :type="input.isActive ? '' : 'is-link'"
              @click="$set(input, 'isActive', !input.isActive)"
              outlined
              :icon-left="input.isActive ? 'pause' : 'play'"
            />
          </b-tooltip>
          <b-tooltip
            label="Prerender"
            :delay="1000"
            type="is-link"
            position="is-bottom"
          >
            <b-button
              rounded
              size="is-small"
              :type="input.offlineRendering ? 'is-link' : ''"
              @click="$set(input, 'offlineRendering', !input.offlineRendering)"
              outlined
              icon-left="rocket-launch"
            />
          </b-tooltip>
          <b-tooltip
            label="Close"
            :delay="1000"
            type="is-link"
            position="is-bottom"
          >
            <b-button
              rounded
              icon-left="close"
              size="is-small"
              @click="$emit('close', id)"
              style="margin-right:unset"
            />
          </b-tooltip>
        </div>
      </div>
      <div class="level">
        <b-tooltip
          label="'channelIdx' property used in Play pattern"
          :delay="1000"
          type="is-link"
          position="is-bottom"
          multilined
          size="is-small"
        >
          <b-input
            size="is-small"
            :value="input.idx"
            @change.native="$set(input, 'idx', $event.target.value)"
            rounded
          />
        </b-tooltip>
        <b-button
          rounded
          icon-left="creation"
          size="is-small"
          @click="addEffect"
          style="margin-left:0.5em; margin-right:unset"
        >
          Add effect
        </b-button>
      </div>
      <div class="subtitle is-6">
        Clips
      </div>
      <Clip :key="`clip-${id}`" v-model="input.clips" />
      <div class="subtitle is-6">Instrument</div>
      <Instrument
        :key="`instrument-${id}`"
        :names="instrumentNames"
        v-model="input.instrument"
      />
      <div v-if="Object.values(input.effects).length">
        <div v-if="Object.values(input.effects).length" class="subtitle is-6">
          Effects
        </div>
        <Instrument
          v-for="(effect, idEffect) in input.effects"
          :key="`effect-${idEffect}`"
          :names="effectNames"
          v-model="input.effects[idEffect]"
          :closeButton="true"
          @close="removeEffect"
          height="100px"
        />
      </div>
    </div>
  </div>
</template>

<script>
import animals from "./animals.json";
import superbs from "./superbs.json";

import Instrument from "./Instrument.vue";
import Clip from "./Clip.vue";

function randomHash() {
  return Math.floor(Math.random() * 0xffffff)
    .toString(16)
    .padStart(6, "0");
}

export default {
  components: { Instrument, Clip },
  props: {
    value: {
      default() {
        return {
          clips: undefined,
          instrument: undefined,
          effects: {},
          offlineRendering: false,
          idx: undefined,
          isActive: true
        };
      }
    }
  },
  data() {
    return {
      input: this.value,
      instrumentNames: [
        "AMSynth",
        "DuoSynth",
        "FMSynth",
        "MembraneSynth",
        "MetalSynth",
        "MonoSynth",
        "NoiseSynth",
        "PluckSynth",
        "Synth"
      ],
      effectNames: [
        "AutoFilter",
        "AutoPanner",
        "AutoWah",
        "BitCrusher",
        "Chebyshev",
        "Chorus",
        "Distortion",
        "FeedbackDelay",
        "Freeverb",
        "FrequencyShifter",
        "JCReverb",
        "Phaser",
        "PingPongDelay",
        "PitchShift",
        "Reverb",
        "StereoWidener",
        "Tremolo",
        "Vibrato"
      ],
      animals,
      superbs
    };
  },
  created() {
    this.input.idx = this.value.idx || this.getRandomIdx();
  },
  computed: {
    id() {
      return this.$vnode.key.split("-")[1];
    }
  },
  methods: {
    addEffect(effect) {
      let id = randomHash();
      this.$set(this.input.effects, id, effect);
    },
    removeEffect(id) {
      this.$delete(this.input.effects, id);
    },
    getRandomIdx() {
      const shortAnimals = animals
        .filter(a => {
          return a.name.split(" ").length == 1;
        })
        .filter(a => {
          return a.name.length <= 6;
        });
      const shortSuperbs = superbs
        .filter(a => {
          return a.split(" ").length == 1;
        })
        .filter(a => {
          return a.length <= 6;
        });
      const animal =
        shortAnimals[Math.floor(Math.random() * shortAnimals.length)];
      const superb =
        shortSuperbs[Math.floor(Math.random() * shortSuperbs.length)];
      return `${animal.emoji} ${superb} ${animal.name}`
        .split(" ")
        .map(w => w[0].toUpperCase() + w.substr(1).toLowerCase())
        .join(" ");
    }
  },
  watch: {
    input: {
      deep: true,
      handler() {
        this.$emit("input", this.input);
      }
    }
  }
};
</script>

<style></style>
