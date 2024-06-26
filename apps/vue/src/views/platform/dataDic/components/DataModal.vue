<template>
  <BasicModal v-bind="$attrs" @register="register" :title="title" @ok="handleSubmit">
    <BasicForm @register="registerForm" />
  </BasicModal>
</template>

<script lang="ts" setup>
  import { ref, nextTick } from 'vue';
  import { useMessage } from '/@/hooks/web/useMessage';
  import { useLocalization } from '/@/hooks/abp/useLocalization';
  import { get, create, update } from '/@/api/platform/datas';
  import { getDateFormSchemas } from './ModalData';
  import { BasicModal, useModalInner } from '/@/components/Modal';
  import { BasicForm, useForm } from '/@/components/Form/index';

  const emits = defineEmits(['register', 'change']);

  const { createMessage } = useMessage();
  const { L } = useLocalization('AppPlatform');
  const schemas = getDateFormSchemas();
  const title = ref('');

  const [registerForm, { validate, setFieldsValue, resetFields }] = useForm({
    labelWidth: 120,
    schemas,
    showActionButtonGroup: false,
    actionColOptions: {
      span: 24,
    },
  });

  const [register, { changeLoading, closeModal }] = useModalInner((dataVal) => {
    nextTick(() => {
      fetchData(dataVal.id);
    });
  });

  function fetchData(id?: string) {
    resetFields();
    if (!id) {
      title.value = L('Data:AddNew');
    } else {
      get(id).then((data) => {
        setFieldsValue(data);
        title.value = L('Data:Edit');
      });
    }
  }

  function handleSubmit() {
    validate().then((input) => {
      changeLoading(true);
      const api = input.id ? update(input.id, input) : create(input);
      api
        .then((data) => {
          createMessage.success(L('Successful'));
          closeModal();
          emits('change', data);
        })
        .finally(() => {
          changeLoading(false);
        });
    });
  }
</script>
