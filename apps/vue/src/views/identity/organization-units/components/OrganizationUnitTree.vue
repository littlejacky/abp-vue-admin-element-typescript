<template>
  <Card :title="L('OrganizationUnit:Tree')">
    <template #extra>
      <a-button
        v-if="hasPermission('AbpIdentity.OrganizationUnits.Create')"
        type="primary"
        pre-icon="ant-design:plus-outlined"
        @click="handleAddNew()"
      >
        {{ L('OrganizationUnit:AddRoot') }}
      </a-button>
    </template>
    <BasicTree
      ref="treeElRef"
      :draggable="true"
      :click-row-to-expand="false"
      :tree-data="ouTree"
      :field-names="{
        title: 'displayName',
        key: 'id',
      }"
      :before-right-click="getContentMenus"
      defaultExpandLevel="1"
      @select="handleSelect"
      @drop="handleDrop"
    />
    <OrganizationUnitModal @register="registerModal" @change="loadOuTree" />
    <PermissionModal @register="registerPermissionModal" />
  </Card>
</template>

<script lang="ts" setup>
  import { Card } from 'ant-design-vue';
  import { usePermission } from '/@/hooks/web/usePermission';
  import { useLocalization } from '/@/hooks/abp/useLocalization';
  import { BasicTree } from '/@/components/Tree';
  import { useModal } from '/@/components/Modal';
  import { useOuTree } from '../hooks/useOuTree';
  import { PermissionModal } from '/@/components/Permission';
  import OrganizationUnitModal from './OrganizationUnitModal.vue';

  const emits = defineEmits(['change', 'select']);

  const { L } = useLocalization('AbpIdentity');
  const [registerModal, modalMethods] = useModal();
  const [registerPermissionModal, permissionModalMethods] = useModal();
  const { ouTree, getContentMenus, handleDrop, handleAddNew, handleSelect, loadOuTree } = useOuTree(
    { emit: emits, modalMethods, permissionModalMethods },
  );
  const { hasPermission } = usePermission();
</script>
