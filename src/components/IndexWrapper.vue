<template>
  <q-page>
    <Suspense>
      <template #default>
        <dashboard class="q-pa-md" />
      </template>
      <template #fallback>
        <div>Loading please wait ...</div>
      </template>
    </Suspense>
    <accounts />
  </q-page>
</template>

<script lang="ts">
import Accounts from 'src/components/Accounts.vue';
import Dashboard from 'src/components/Dashboard.vue';
import { provide, defineComponent, reactive } from 'vue';
import Web3 from 'web3';
import { oracleAddress, stakingTokenAddress } from '../address';

export default defineComponent({
  components: { Dashboard, Accounts },
  name: 'IndexWrapper',
  async setup() {
    const web3 = new Web3(
      Web3.givenProvider || 'https://bsc-dataseed1.defibit.io/'
    );
    const oracle = reactive({});
    const skillWallet = reactive({});

    provide('web3', web3);
    provide('oracle', oracle);
    provide('skillWallet', skillWallet);

    /* eslint-disable @typescript-eslint/no-unsafe-assignment */
    let res = await fetch('/contracts/BasicPriceOracle.json');
    const oracleJSON = await res.json();
    /* eslint-disable @typescript-eslint/no-unsafe-member-access */
    const _oracle = new web3.eth.Contract(oracleJSON.abi, oracleAddress);
    Object.assign(oracle, _oracle);

    res = await fetch('/contracts/IERC20.json');
    const skillWalletJSON = await res.json();
    const _skillWallet = new web3.eth.Contract(
      skillWalletJSON.abi,
      stakingTokenAddress
    );
    Object.assign(skillWallet, _skillWallet);

    return;
  },
});
</script>
