<template>
  <div class="q-py-md row q-gutter-lg justify-center">
    <div class="col-xs-12 col-md-2">
      <card :label="'Oracle'" :content="oraclePrice" />
    </div>
    <div class="col-xs-12 col-md-2">
      <card :label="'Skill'" :content="skillPrice" />
    </div>
  </div>
</template>

<script lang="ts">
/* eslint-disable @typescript-eslint/no-unsafe-assignment */
/* eslint-disable @typescript-eslint/no-unsafe-member-access */
/* eslint-disable @typescript-eslint/no-unsafe-call */

import Card from './Card.vue';
import { defineComponent, inject, ref } from 'vue';
import { defaultAddress } from '../address';
export default defineComponent({
  name: 'Dashboard',
  components: { Card },
  async setup() {
    const web3: any = inject('web3');
    const oracle: any = inject('oracle');

    const oraclePrice = ref(0);
    const skillPrice = ref(0);

    const getOraclePrice = async () => {
      const price = await oracle.methods
        .currentPrice()
        .call({ from: defaultAddress });
      oraclePrice.value = Number(
        (1 / Number(web3.utils.fromWei(price, 'ether'))).toFixed(2)
      );
    };

    const getSkillPrice = async () => {
      const res = await fetch(
        'https://api.coingecko.com/api/v3/simple/price?ids=cryptoblades,binancecoin,tether&vs_currencies=usd'
      );
      const json = await res.json();
      skillPrice.value = json.cryptoblades.usd;
    };

    await Promise.all([getOraclePrice(), getSkillPrice()]);

    return { oraclePrice, skillPrice };
  },
});
</script>

<style></style>
