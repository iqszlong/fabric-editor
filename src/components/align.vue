<!--
 * @Author: 秦少卫
 * @Date: 2022-09-03 19:16:55
 * @LastEditors: 秦少卫
 * @LastEditTime: 2024-10-07 17:26:59
 * @Description: 组合元素对齐
-->

<template>
  <div v-if="isMultiple" class="attr-item-box">
    <!-- <h3>对齐</h3> -->
    <Divider plain orientation="left" size="small"><h4>对齐</h4></Divider>
    <div class="bg-item">
      <!-- 水平对齐 -->
      <Tooltip :content="$t('attrSeting.align.left')">
        <Button @click="left" size="small" type="text">
          <leftIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.centerX')">
        <Button @click="xcenter" size="small" type="text">
          <centerxIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.right')">
        <Button @click="right" size="small" type="text">
          <rightIcon />
        </Button>
      </Tooltip>
      <!-- 垂直对齐 -->
      <Tooltip :content="$t('attrSeting.align.top')">
        <Button @click="top" size="small" type="text">
          <topIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.centerY')">
        <Button @click="ycenter" size="small" type="text">
          <centeryIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.bottom')">
        <Button @click="bottom" size="small" type="text">
          <bottomIcon />
        </Button>
      </Tooltip>
      <!-- 平均对齐 -->
      <Tooltip :content="$t('attrSeting.align.averageX')">
        <Button @click="xequation" size="small" type="text">
          <sxIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.averageY')">
        <Button @click="yequation" size="small" type="text">
          <syIcon />
        </Button>
      </Tooltip>
    </div>
    <!-- <Divider plain></Divider> -->
  </div>
  <div v-else-if="isOne" class="attr-item-box">
    <Divider plain orientation="left" size="small"><h4>对齐</h4></Divider>
    <div class="bg-item">
      <Tooltip :content="$t('attrSeting.align.left')">
        <Button long @click="left" type="text">
          <leftIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.right')">
        <Button long @click="right" type="text">
          <rightIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.top')">
        <Button long @click="top" type="text">
          <topIcon />
        </Button>
      </Tooltip>
      <Tooltip :content="$t('attrSeting.align.bottom')">
        <Button long @click="bottom" type="text">
          <bottomIcon />
        </Button>
      </Tooltip>
    </div>
  </div>
</template>

<script name="Align" setup>
import useSelect from '@/hooks/select';

import leftIcon from '@/assets/icon/left.svg';
import rightIcon from '@/assets/icon/right.svg';

import topIcon from '@/assets/icon/top.svg';
import bottomIcon from '@/assets/icon/bottom.svg';

import sxIcon from '@/assets/icon/sx.svg';
import syIcon from '@/assets/icon/sy.svg';

import centerxIcon from '@/assets/icon/centerx.svg';
import centeryIcon from '@/assets/icon/centery.svg';

const { canvasEditor, isMultiple, isOne } = useSelect();

// 左对齐
const left = () => {
  if (isOne) {
    setValue('left');
    return;
  }
  canvasEditor.left();
};
// 右对齐
const right = () => {
  if (isOne) {
    setValue('right');
    return;
  }
  canvasEditor.right();
};
// 水平居中对齐
const xcenter = () => {
  canvasEditor.xcenter();
};
// 垂直居中对齐
const ycenter = () => {
  canvasEditor.ycenter();
};
// 顶部对齐
const top = () => {
  if (isOne) {
    setValue('top');
    return;
  }
  canvasEditor.top();
};
// 底部对齐
const bottom = () => {
  if (isOne) {
    setValue('bottom');
    return;
  }
  canvasEditor.bottom();
};
// 水平平均对齐
const xequation = () => {
  canvasEditor.xequation();
};
// 垂直平均对齐
const yequation = () => {
  canvasEditor.yequation();
};

const setValue = (key, value = 0) => {
  const activeObject = canvasEditor.canvas.getActiveObject();
  // console.log(canvasEditor.getWorkspase());
  const size = canvasEditor.getWorkspase();
  const { width, height } = size || {};
  if (key === 'right') {
    value = width - activeObject.getScaledWidth();
    key = 'left';
  }
  if (key === 'bottom') {
    value = height - activeObject.getScaledHeight();
    key = 'top';
  }
  activeObject && activeObject.set(key, value);
  canvasEditor.canvas.renderAll();
};
</script>

<style scoped lang="less">
.icon {
  max-width: 100%;
  height: 24px;
}
</style>
