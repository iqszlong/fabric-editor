<template>
  <div class="box attr-item-box" v-if="isOne">
    <Divider plain orientation="left" size="small"><h4>基本属性</h4></Divider>
    <Space direction="vertical" type="flex" v-show="isMatchType">
      <Row :gutter="10">
        <Col flex="1">
          <Input
            v-model="baseAttr.name"
            @on-change="(event) => changeCommon('name', event.target.value)"
          >
            <template #prepend>
              <span>名称</span>
            </template>
          </Input>
        </Col>
      </Row>
      <Row :gutter="10">
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.width"
            @on-change="(value) => changeCommon('width', value)"
            :append="$t('attributes.width')"
          ></InputNumber>
        </Col>
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.height"
            @on-change="(value) => changeCommon('height', value)"
            :append="$t('attributes.height')"
          ></InputNumber>
        </Col>
      </Row>
      <Row :gutter="10">
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.scaleX"
            @on-change="(value) => changeCommon('scaleX', value)"
            :append="$t('attributes.scale_x')"
          ></InputNumber>
        </Col>
        <Col flex="1">
          <InputNumber
            v-model="baseAttr.scaleY"
            @on-change="(value) => changeCommon('scaleY', value)"
            :append="$t('attributes.scale_y')"
          ></InputNumber>
        </Col>
      </Row>
    </Space>
  </div>
</template>

<script setup name="AttrBase">
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
  name: '',
  width: 0,
  height: 0,
  scaleX: 1,
  scaleY: 1,
});

// 属性获取
const getObjectAttr = (e) => {
  const activeObject = canvasEditor.canvas.getActiveObject();
  // 不是当前obj，跳过
  if (e && e.target && e.target !== activeObject) return;
  if (activeObject && isMatchType) {
    baseAttr.name = activeObject.get('name');
    baseAttr.width = activeObject.get('width');
    baseAttr.height = activeObject.get('height');
    baseAttr.scaleX = activeObject.get('scaleX');
    baseAttr.scaleY = activeObject.get('scaleY');
  }
};

const selectCancel = () => {
  update?.proxy?.$forceUpdate();
};

// 通用属性改变
const changeCommon = (key, value) => {
  const activeObject = canvasEditor.canvas.getActiveObjects()[0];
  if (activeObject) {
    activeObject && activeObject.set(key, value);
    canvasEditor.canvas.renderAll();
  }
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

<style lang="less" scoped>
:deep(.ivu-input-number) {
  display: block;
  width: 100%;
}
</style>
