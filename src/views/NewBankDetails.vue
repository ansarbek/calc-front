<template>
  <d-container fluid class="main-content-container px-4 pb-4">
    <!-- Page Header -->
    <d-row no-gutters class="page-header py-4">
      <!-- Page Title -->
      <d-col col sm="4" class="text-center text-sm-left mb-4 mb-sm-0">
        <span class="text-uppercase page-subtitle">Компания</span>
        <h3 class="page-title">Счета компании</h3>
      </d-col>
    </d-row>

    <d-row>
      <!-- Editor -->
      <d-col lg="12" md="12">
        <bank-detail/>
      </d-col>
    </d-row>
  </d-container>
</template>

<script>
import BankDetail from '@/components/bank-details/BankDetail.vue';

export default {
  components: {
    BankDetail,
  },
};
</script>
