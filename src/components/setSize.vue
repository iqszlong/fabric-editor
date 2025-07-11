<!--
 * @Author: 秦少卫
 * @Date: 2022-09-03 19:16:55
 * @LastEditors: June
 * @LastEditTime: 2024-11-22 15:28:43
 * @Description: 尺寸设置
-->

<template>
  <div v-if="!isSelect" class="attr-item-box">
    <!-- <h3>{{ $t('bgSeting.size') }}</h3> -->
    <!-- <Divider plain orientation="left">
      <h4>{{ $t('bgSeting.size') }}</h4>
    </Divider> -->
    <Space direction="vertical" type="flex">
      <Form class="form-wrap">
        <FormItem prop="name">
          <InputNumber
            v-model="width"
            @on-change="setSize"
            :append="$t('bgSeting.width')"
          ></InputNumber>
        </FormItem>
        <FormItem>
          <Tooltip :content="$t('bgSeting.swap')">
            <Button long @click="swapSize" type="text">
              <Icon type="md-swap" />
            </Button>
          </Tooltip>
        </FormItem>
        <FormItem prop="name">
          <InputNumber
            v-model="height"
            @on-change="setSize"
            :append="$t('bgSeting.height')"
          ></InputNumber>
        </FormItem>
      </Form>
      <Button long @click="showSetSize" size="large">
        <Icon type="md-create" />
        {{ $t('setSizeTip') }}
      </Button>
    </Space>
    <!-- <Divider plain></Divider> -->
    <!-- 修改尺寸 -->
    <modalSzie :title="$t('setSizeTip')" ref="modalSizeRef" @set="handleConfirm"></modalSzie>
  </div>
</template>

<script setup name="CanvasSize">
import useSelect from '@/hooks/select';
import modalSzie from '@/components/common/modalSzie';
import InputNumber from '@/components/inputNumber';

const { isSelect, canvasEditor } = useSelect();

const modalSizeRef = ref(null);

const width = ref(0);
const height = ref(0);

onMounted(() => {
  const size = canvasEditor.getWorkspase();
  const { width: w, height: h } = size || {};
  width.value = w;
  height.value = h;
  canvasEditor.on('sizeChange', (w, h) => {
    width.value = w;
    height.value = h;
  });
});

const setSize = () => {
  canvasEditor.setSize(width.value, height.value);
};

const showSetSize = () => {
  modalSizeRef.value.showSetSize(width.value, height.value);
};
const handleConfirm = (w, h) => {
  width.value = w;
  height.value = h;
  setSize();
};

const swapSize = () => {
  const temp = width.value;
  width.value = height.value;
  height.value = temp;
  setSize();
};
</script>

<style scoped lang="less">
:deep(.ivu-form-item) {
  margin-bottom: 0;
}

:deep(.ivu-input-number) {
  display: block;
  width: 100%;
}
.form-wrap {
  display: flex;
}
</style>
