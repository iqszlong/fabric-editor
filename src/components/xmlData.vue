<template>
  <div v-if="isOne && isMatchType" class="box attr-item-box">
    <Divider plain orientation="left" size="small"><h4>XML</h4></Divider>
    <Space direction="vertical" type="flex">
      <Select v-model="coordinate" @on-change="setXmlCode">
        <template #prefix>坐标基准：</template>
        <Option v-for="item in coordinatelist" :value="item.value" :key="item.value">
          {{ item.label }}
        </Option>
      </Select>
      <Input
        ref="xmlInputRef"
        v-model="xmlCode"
        type="textarea"
        :rows="4"
        placeholder=""
        readonly
        :autosize="{ minRows: 4, maxRows: 10 }"
        @on-focus="focusAll"
      />
    </Space>
  </div>
</template>

<script setup name="xmlData">
import useSelect from '@/hooks/select';

const update = getCurrentInstance();
// 可监听的元素
const baseType = ['i-text', 'textbox', 'image', 'group'];
const { isMatchType, canvasEditor, isOne } = useSelect(baseType);

const xmlInputRef = ref(null);

// 属性值
const baseAttr = reactive({
  name: '',
  type: null,
  width: 0,
  height: 0,
  left: 0,
  top: 0,
  cLeft: 0,
  cTop: 0,
  fontSize: 0,
  angle: 0,
});

const coordinate = ref('center');

const coordinatelist = [
  {
    value: 'center',
    label: '中心点',
  },
  {
    value: 'lt',
    label: '左上角起始点',
  },
];

const xmlCode = ref('');

// 属性获取
const getObjectAttr = (e) => {
  const activeObject = canvasEditor.canvas.getActiveObject();
  const center = canvasEditor.canvas.getCenterPoint();
  // 不是当前obj，跳过
  if (e && e.target && e.target !== activeObject) return;
  if (activeObject && isMatchType) {
    baseAttr.name = activeObject.get('name') ?? activeObject.get('text') ?? '';
    baseAttr.type = activeObject.get('type');
    baseAttr.width = activeObject.get('width');
    baseAttr.height = activeObject.get('height');
    baseAttr.left = activeObject.get('left');
    baseAttr.top = activeObject.get('top');
    baseAttr.cTop = activeObject.get('top') - center.y;
    baseAttr.cLeft = activeObject.get('left') - center.x;
    baseAttr.fontSize = activeObject.get('fontSize');
    baseAttr.angle = activeObject.get('angle');

    setXmlCode();
  }
};

const selectCancel = () => {
  update?.proxy?.$forceUpdate();
};

const setXmlCode = () => {
  let x = 0;
  let y = 0;
  if (coordinate.value === 'center') {
    x = baseAttr.cLeft;
    y = baseAttr.cTop;
  } else {
    x = baseAttr.left;
    y = baseAttr.top;
  }
  if (baseAttr.type === 'group') {
    xmlCode.value = `<${baseAttr.type} x="${x}" y="${y}" angle="${baseAttr.angle}"></${baseAttr.type}>`;
  }
  if (baseAttr.type === 'i-text' || baseAttr.type === 'textbox') {
    xmlCode.value = `<Text text="${baseAttr.name.trim().replaceAll('\n', '')}" 
    size="${baseAttr.fontSize}" x="${x}" y="${y}" angle="${baseAttr.angle}"
     w="${baseAttr.width}" h="${baseAttr.height}" />`;
  }
  if (baseAttr.type === 'image') {
    xmlCode.value = `<${baseAttr.type} src="${baseAttr.name}" 
    x="${x}" y="${y}" w="${baseAttr.width}" h="${baseAttr.height}" angle="${baseAttr.angle}">
    </${baseAttr.type}>`;
  }
};

const focusAll = () => {
  xmlInputRef.value.focus({ cursor: 'all' });
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

<style lang="less" scoped></style>
