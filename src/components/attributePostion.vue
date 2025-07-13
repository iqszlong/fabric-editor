<!--
 * @Author: 秦少卫
 * @Date: 2024-05-21 09:23:36
 * @LastEditors: 秦少卫
 * @LastEditTime: 2024-10-07 17:33:41
 * @Description: file content
-->
<template>
  <div class="box attr-item-box" v-if="isOne">
    <!-- <h3>位置信息</h3> -->
    <Divider plain orientation="left" size="small"><h4>位置信息</h4></Divider>
    <!-- 通用属性 -->
    <Space direction="vertical" type="flex" v-show="isMatchType">
      <Row :gutter="10">
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.left"
            @on-change="(value) => changeCommon('left', value)"
            :append="$t('attributes.left')"
          ></InputNumber>
        </Col>
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.top"
            @on-change="(value) => changeCommon('top', value)"
            :append="$t('attributes.top')"
          ></InputNumber>
        </Col>
      </Row>
      <Row :gutter="10">
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.cLeft"
            @on-change="(value) => changeCommon('cleft', value)"
            :append="$t('attributes.c_left')"
          ></InputNumber>
        </Col>
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.cTop"
            @on-change="(value) => changeCommon('ctop', value)"
            :append="$t('attributes.c_top')"
          ></InputNumber>
        </Col>
      </Row>
      <Button long @click="xyTointeger">{{ $t('attributes.to_integer') }}</Button>
      <Form :label-width="40" class="form-wrap">
        <FormItem :label="$t('attributes.angle')">
          <Slider
            v-model="baseAttr.angle"
            :max="360"
            @on-input="(value) => changeCommon('angle', value)"
          ></Slider>
        </FormItem>
        <FormItem :label="$t('attributes.opacity')">
          <Slider
            v-model="baseAttr.opacity"
            @on-input="(value) => changeCommon('opacity', value)"
          ></Slider>
        </FormItem>
      </Form>
    </Space>
    <!-- <Divider plain></Divider> -->
  </div>
</template>

<script setup name="AttrBute">
import useSelect from '@/hooks/select';
import InputNumber from '@/components/inputNumber';

const update = getCurrentInstance();

// 可修改的元素
const baseType = [
  'text',
  'i-text',
  'textbox',
  'rect',
  'circle',
  'triangle',
  'polygon',
  'image',
  'group',
  'line',
  'arrow',
  'thinTailArrow',
];
const { isMatchType, canvasEditor, isOne } = useSelect(baseType);

// 属性值
const baseAttr = reactive({
  opacity: 0,
  angle: 0,
  left: 0,
  top: 0,
  rx: 0,
  ry: 0,
  cLeft: 0,
  cTop: 0,
});

// 属性获取
const getObjectAttr = (e) => {
  const activeObject = canvasEditor.canvas.getActiveObject();
  const center = canvasEditor.canvas.getCenterPoint();
  // 不是当前obj，跳过
  if (e && e.target && e.target !== activeObject) return;
  if (activeObject && isMatchType) {
    baseAttr.opacity = activeObject.get('opacity') * 100;
    baseAttr.left = activeObject.get('left');
    baseAttr.top = activeObject.get('top');
    baseAttr.angle = activeObject.get('angle') || 0;
    baseAttr.cTop = activeObject.get('top') - center.y;
    baseAttr.cLeft = activeObject.get('left') - center.x;
  }
};

// 通用属性改变
const changeCommon = (key, value) => {
  const activeObject = canvasEditor.canvas.getActiveObjects()[0];
  const center = canvasEditor.canvas.getCenterPoint();
  if (activeObject) {
    // 透明度特殊转换
    if (key === 'opacity') {
      value = value / 100;
    }
    // 旋转角度适配
    if (key === 'angle') {
      activeObject.rotate(value);
      canvasEditor.canvas.renderAll();
      return;
    }
    if (key === 'cleft') {
      // key = 'left';
      value = value + center.x;
    }
    if (key === 'ctop') {
      // key = 'top';
      value = value + center.y;
    }
    console.log(key, value);

    activeObject && activeObject.set(key, value);
    canvasEditor.canvas.renderAll();
  }
};

const selectCancel = () => {
  update?.proxy?.$forceUpdate();
};

const xyTointeger = () => {
  baseAttr.left = Math.round(baseAttr.left);
  baseAttr.top = Math.round(baseAttr.top);
  baseAttr.cLeft = Math.round(baseAttr.cLeft);
  baseAttr.cTop = Math.round(baseAttr.cTop);
  changeCommon('left', baseAttr.left);
  changeCommon('top', baseAttr.top);
  changeCommon('cleft', baseAttr.cLeft);
  changeCommon('ctop', baseAttr.cTop);
};

onMounted(() => {
  // 获取字体数据
  getObjectAttr();
  canvasEditor.on('selectCancel', selectCancel);
  canvasEditor.on('selectOne', getObjectAttr);
  canvasEditor.canvas.on('object:modified', getObjectAttr);
});

onBeforeUnmount(() => {
  canvasEditor.off('selectCancel', selectCancel);
  canvasEditor.off('selectOne', getObjectAttr);
  canvasEditor.canvas.off('object:modified', getObjectAttr);
});
</script>

<style scoped lang="less">
:deep(.ivu-input-number) {
  display: block;
  width: 100%;
}

.ivu-form-item {
  background: #f6f7f9;
  border-radius: 5px;
  padding: 0 5px;
  margin-bottom: 10px;
}
</style>
