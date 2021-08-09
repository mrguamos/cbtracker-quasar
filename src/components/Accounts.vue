<template>
  <div class="q-pt-md q-pb-md">
    <q-table
      title="Accounts"
      :columns="cols"
      row-key="address"
      :rows="accounts"
    >
      <template v-slot:body-cell-action="props">
        <q-td :props="props">
          <q-btn
            round
            color="primary"
            icon="delete"
            @click="removeAccount(props.rowIndex)"
          /> </q-td
      ></template>
    </q-table>

    <div class="row q-pt-md">
      <q-space />
      <q-btn round color="primary" icon="add" @click="prompt = true" />
    </div>

    <q-dialog v-model="prompt" persistent>
      <q-card style="width: 700px; max-width: 80vw">
        <q-card-section>
          <div class="text-h6">Your address</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-form class="q-gutter-md">
            <q-input
              filled
              v-model="account.name"
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
    label: 'TOTAL SKILL',
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
    const web3: any = inject('web3');
    const skillWalletContract: any = inject('skillWallet');
    const prompt = ref(false);
    const account: MetaAccount = reactive(defaultAccount);

    const storedAccounts: MetaAccount[] = JSON.parse(
      localStorage.getItem('metamask-cb-accounts')!
    );

    const accounts: Ref<MetaAccount[]> = storedAccounts
      ? ref(storedAccounts)
      : ref([]);

    const fetchAccounts = async () => {
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
            console.log(skillwallet);
          } catch (error) {
            console.log('Invalid Address');
          }
        })
      );
    };

    void fetchAccounts();

    const onCancel = () => {
      cleanForm();
      prompt.value = false;
    };

    const onSubmit = () => {
      const a: MetaAccount = Object.assign({}, account);
      accounts.value.push(a);
      localStorage.setItem(
        'metamask-cb-accounts',
        JSON.stringify(unref(accounts))
      );
      cleanForm();
      prompt.value = false;
    };

    const removeAccount = (index: number) => {
      accounts.value.splice(index, 1);
      localStorage.setItem(
        'metamask-cb-accounts',
        JSON.stringify(unref(accounts))
      );
    };
    const cleanForm = () => {
      account.name = '';
      account.address = '';
    };

    return {
      removeAccount,
      accounts,
      cols,
      account,
      prompt,
      onCancel,
      onSubmit,
    };
  },
});
</script>

<style></style>
