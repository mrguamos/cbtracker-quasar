<template>
  <div class="q-pt-md q-pb-md">
    <q-table
      title="Accounts"
      :columns="cols"
      row-key="address"
      :rows="accounts"
      :loading="loading"
      @row-click="onRowClick"
    >
      <template v-slot:body-cell-action="props">
        <q-td :props="props" @click.stop>
          <q-btn
            round
            color="primary"
            icon="delete"
            @click="showDeleteModal(props.row.name, props.rowIndex)"
          /> </q-td
      ></template>
    </q-table>

    <div class="row q-pt-md">
      <q-space />
      <div class="q-gutter-md">
        <q-btn round color="primary" icon="refresh" @click="fetchAccounts()" />
        <q-btn round color="primary" icon="add" @click="accountModal = true" />
      </div>
    </div>
    <q-dialog v-model="deleteModal" persistent>
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar icon="delete" color="red" text-color="white" />
          <span class="q-ml-sm"
            >Are you sure you want to delete this account?
            <span class="text-weight-bolder">{{ accountName }}</span></span
          >
        </q-card-section>

        <q-card-actions align="right">
          <q-btn flat label="Cancel" color="primary" v-close-popup />
          <q-btn flat label="Yes" color="primary" @click="removeAccount()" />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <q-dialog v-model="accountModal" persistent>
      <q-card style="width: 700px; max-width: 80vw">
        <q-card-section>
          <div class="text-h6">Your address</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-form class="q-gutter-md">
            <q-input
              filled
              v-model="account.name"
              name="name"
              label="Account Name"
              hint="Metamask Account Name"
              lazy-rules
              :rules="[
                (val) => (val && val.length > 0) || 'Please type something',
              ]"
            />

            <q-input
              filled
              v-model="account.address"
              label="Account Address"
              hint="Metamask Account Address"
              name="address"
              lazy-rules
              :rules="[
                (val) => (val && val.length > 0) || 'Please type something',
              ]"
            />
          </q-form>
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancel" @click="onCancel()" />
          <q-btn flat label="Submit" @click="onSubmit()" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script lang="ts">
/* eslint-disable @typescript-eslint/no-unsafe-member-access */
// eslint-disable-next-line @typescript-eslint/no-unsafe-assignment
/* eslint-disable @typescript-eslint/no-unsafe-assignment */
/* eslint-disable @typescript-eslint/no-unsafe-call */

import { ref, defineComponent, reactive, Ref, inject, unref, toRaw } from 'vue';
import { defaultAddress } from '../address';

const cols = [
  {
    label: 'ACCOUNT',
    align: 'left',
    field: 'name',
  },
  {
    label: 'ADDRESS',
    field: 'address',
  },
  {
    label: 'WALLET SKILL',
    field: 'skillWallet',
  },
  {
    label: 'ACTIONS',
    name: 'action',
  },
];

const defaultAccount: MetaAccount = {
  name: '',
  address: '',
};

export default defineComponent({
  name: 'Accounts',
  setup() {
    const loading = ref(true);
    const web3: any = inject('web3');
    const skillWalletContract: any = inject('skillWallet');
    const accountModal = ref(false);
    const deleteModal = ref(false);
    const account: MetaAccount = reactive(defaultAccount);
    const accountName = ref('');
    const index = ref(0);

    const storedAccounts: MetaAccount[] = JSON.parse(
      localStorage.getItem('metamask-cb-accounts')!
    );

    const onRowClick = (e: Event, row: number) => {
      console.log(e, row);
    };

    const accounts: Ref<MetaAccount[]> = storedAccounts
      ? ref(storedAccounts)
      : ref([]);

    const fetchAccounts = async () => {
      loading.value = true;
      await Promise.all(
        accounts.value.map(async (account) => {
          try {
            let skillwallet = Number(
              await skillWalletContract.methods
                .balanceOf(account.address)
                .call({ from: defaultAddress })
            );
            skillwallet = Number(
              parseFloat(
                web3.utils.fromWei(BigInt(skillwallet).toString(), 'ether')
              ).toFixed(6)
            );
            account.skillWallet = skillwallet;
          } catch (error) {
            console.log('Invalid Address');
          }
        })
      );
      loading.value = false;
    };

    void fetchAccounts();

    const showDeleteModal = (an: string, i: number) => {
      accountName.value = an;
      deleteModal.value = true;
      index.value = i;
    };

    const onCancel = () => {
      cleanForm();
      accountModal.value = false;
    };

    const onSubmit = async () => {
      const a: MetaAccount = Object.assign({}, account);
      accounts.value.push(a);
      await fetchAccounts();
      localStorage.setItem(
        'metamask-cb-accounts',
        JSON.stringify(unref(accounts))
      );
      cleanForm();
      accountModal.value = false;
    };

    const removeAccount = () => {
      console.log(index.value);
      accounts.value.splice(index.value, 1);
      localStorage.setItem(
        'metamask-cb-accounts',
        JSON.stringify(unref(accounts))
      );
      deleteModal.value = false;
    };
    const cleanForm = () => {
      account.name = '';
      account.address = '';
    };

    return {
      showDeleteModal,
      accountName,
      onRowClick,
      fetchAccounts,
      removeAccount,
      accounts,
      cols,
      account,
      accountModal,
      onCancel,
      onSubmit,
      loading,
      deleteModal,
    };
  },
});
</script>

<style></style>
