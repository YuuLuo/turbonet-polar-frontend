<script setup>
import ShowUserPermission from "@/layouts/turboPremission/ShowTurboPermission.vue";
import {sendGetRequest} from "@/assets/js/RequestHandler.js";
import {onMounted, ref} from "vue";
import UpgradeTurboPermission from "@/layouts/turboPremission/UpgradeTurboPermission.vue";
import WarningandBanPage from "@/layouts/turboPremission/ExecutePage.vue";
import {useI18n} from "vue-i18n";

const { t } = useI18n()
const isLoading = ref(true);
const isSuccess = ref(false)
const responseData = ref()

const showUserPermission = async () => {
  await sendGetRequest('/web/turboPermission', true).then((response) => {
    if (response.statusCode === 200) {
      isLoading.value = false
      isSuccess.value = true
      responseData.value = response.data
    } else {
      isLoading.value = false
      isSuccess.value = false
      responseData.value = response.data
    }
  }).catch(() => {
    isLoading.value = false
    isSuccess.value = false
    responseData.value = t('error.jsError')
  })
}

onMounted(() => {
  showUserPermission()
})

</script>

<template>
  <div v-if="isLoading || !isSuccess" class="main-container-center">
    <span v-if="isLoading" class="loading loading-spinner size-8"/>
    <div v-if="!isLoading && !isSuccess">
      <h1 class="font-bold text-3xl">
        <i class="fa-regular fa-circle-xmark"></i> {{ $t('error.loadingError') }}
      </h1>
      <div class="mt-3"></div>
      <p>
        {{ responseData }} <router-link class="text-primary" to="/">{{ $t('error.back') }}</router-link>
      </p>
    </div>
  </div>
  <div v-else>
    <div class="breadcrumbs text-sm">
      <ul>
        <i class="fa-solid fa-link"></i>&nbsp;&nbsp;
        <li>{{ $t('menu.turboPermission') }}</li>
      </ul>
    </div>
    <div class="mt-2"/>
    <div role="tablist" class="tabs tabs-lifted">
      <input type="radio" name="my_tabs_2" role="tab" class="tab" :aria-label="$t('turboPermission.myPermission')" checked="checked"/>
      <div role="tabpanel" class="tab-content bg-base-100 border-base-300 rounded-box p-6">
        <ShowUserPermission :turbo-permission-info="responseData"/>
      </div>

      <input
          type="radio"
          name="my_tabs_2"
          role="tab"
          class="tab"
          :aria-label="$t('turboPermission.upgradePermission')"
          :disabled="responseData.permission !== 'BUILDER' && responseData.permission !== 'ADMIN'"
      />
      <div role="tabpanel" class="tab-content bg-base-100 border-base-300 rounded-box p-6">
        <UpgradeTurboPermission :turbo-permission="responseData.permission"/>
      </div>

      <input
          type="radio"
          name="my_tabs_2"
          role="tab"
          class="tab"
          :aria-label="$t('turboPermission.warningAndBan')"
          :disabled="responseData.permission !== 'ADMIN'"
      />
      <div role="tabpanel" class="tab-content bg-base-100 border-base-300 rounded-box p-6">
        <WarningandBanPage/>
      </div>
    </div>
  </div>
</template>

<style scoped>
.tabs {
  flex-direction: row !important;
}

.tab {
  writing-mode: horizontal-tb !important;
  text-align: center !important;
  white-space: nowrap !important; /* 确保文字不换行 */
}

</style>