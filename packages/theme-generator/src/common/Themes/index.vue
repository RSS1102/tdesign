<template>
  <div class="recommend-theme">
    <div :key="idx" v-for="(type, idx) in recommendThemes">
      <div class="recommend-theme__title">
        {{ lang.dock.recommendTitle }}
      </div>
      <div class="recommend-theme__flex">
        <div v-for="(theme, themeIdx) in type.options" :key="themeIdx" @click="generateNewTheme(theme)">
          <div
            class="recommend-theme__flex-theme"
            :style="{
              'background-color': theme.value,
            }"
          >
            <div v-html="theme.subtitle" />
            <div v-if="currentTheme && currentTheme.value === theme.value" class="recommend-theme__flex-theme--active">
              <picked-svg />
            </div>
          </div>
          <p
            :style="{
              margin: '4px 0',
              'text-align': 'center',
              'font-size': '12px',
              'line-height': '20px',
            }"
          >
            {{ isEn ? theme.enName : theme.name }}
          </p>
        </div>

        <t-upload :requestMethod="requestMethod" @success="onUploadSuccess" theme="custom">
          <div
            class="recommend-theme__flex-theme"
            :style="{
              'background-color': defaultTheme.value,
              display: 'flex',
              'justify-content': 'center',
              'align-items': 'center',
            }"
          >
            <add-rectangle />
          </div>
          <p
            :style="{
              margin: '4px 0',
              'text-align': 'center',
              'font-size': '12px',
              'line-height': '20px',
            }"
          >
            自定义Theme
          </p>
        </t-upload>
      </div>
    </div>
  </div>
</template>

<script>
import { RECOMMEND_THEMES, defaultTheme } from './const';
import { generateNewTheme } from '../utils';
import PickedSvg from './svg/PickedSvg.vue';
import AddRectangle from './svg/AddRectangle.vue';
import langMixin from '../i18n/mixin';
import { Upload } from 'tdesign-vue';

export default {
  emit: ['changeTabTheme'],
  props: {
    currentTheme: Object,
  },
  mixins: [langMixin],
  data() {
    return {
      recommendThemes: RECOMMEND_THEMES,
      defaultTheme,
      isThemeTabVisible: false,
      isDrawerVisible: false,
      uploadMethod: 'requestSuccessMethod',
    };
  },
  components: {
    PickedSvg,
    AddRectangle,
    TUpload: Upload,
  },
  methods: {
    generateNewTheme(theme) {
      generateNewTheme(theme.value);
      this.$emit('changeTabTheme', theme);
    },
    requestMethod(file) {
      return new Promise((resolve) => {
        if (file.type !== 'text/css') {
          resolve({ status: 'error', response: { files: [] } });
        }
        resolve({
          status: 'success',
          response: { files: [file] },
        });
      });
    },
    onUploadSuccess(content) {
      console.log(content);
      const reader = new FileReader();
      reader.readAsText(content.file.raw);
      reader.onload = (e) => {
        const css = e.target.result;
        const style = document.getElementById('custom-theme');
        if (style) {
          style.innerText = css;
        }
      };
    },
  },
};
</script>

<style lang="less" scoped>
.recommend-theme {
  max-height: 376px;
  border-radius: 32px 32px 0px 0px;

  &__content {
    padding: 0;
    overflow: auto;

    &:hover {
      &::-webkit-scrollbar-thumb {
        background-color: var(--bg-color-scroll);
      }
    }

    &::-webkit-scrollbar {
      width: 12px;
      background: transparent;
    }

    &::-webkit-scrollbar-thumb {
      border-radius: 6px;
      border: 4px solid transparent;
      background-clip: content-box;
      background-color: transparent;
    }
  }

  &__main {
    padding: 12px 4px 16px 16px;
  }

  &__title {
    color: var(--text-primary);
    font-weight: 600;
    font-size: 14px;
    margin: 8px 0px 0px 12px;
    line-height: 22px;
    display: flex;
    align-items: center;
  }

  &__flex {
    display: flex;
    flex-wrap: wrap;
    margin: 4px -4px;
    cursor: pointer;

    > div {
      margin: 4px;
      padding: 6px 6px 0px 6px;
      color: var(--text-primary);
      background: var(--bg-color-card);
      border-radius: 18px;
      transition: all 0.2s cubic-bezier(0.38, 0, 0.24, 1);

      &:hover {
        scale: 1.05;
      }
    }

    &-theme {
      width: 78px;
      height: 62px;
      padding: 8px 6px;
      border-radius: 12px;
      position: relative;
      overflow: hidden;

      &--active {
        position: absolute;
        right: 0px;
        top: 30px;
      }
    }
  }
}
</style>
