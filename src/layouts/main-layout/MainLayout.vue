<template>
  <div id="kt_app_content" class="app-content flex-column-fluid">
    <KTContent></KTContent>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  nextTick,
  onBeforeMount,
  onMounted,
  watch,
} from "vue";
import KTHeader from "@/layouts/main-layout/header/Header.vue";
import KTSidebar from "@/layouts/main-layout/sidebar/Sidebar.vue";
import KTContent from "@/layouts/main-layout/content/Content.vue";
import KTToolbar from "@/layouts/main-layout/toolbar/Toolbar.vue";
import KTFooter from "@/layouts/main-layout/footer/Footer.vue";
import KTDrawers from "@/layouts/main-layout/drawers/Drawers.vue";
import KTModals from "@/layouts/main-layout/modals/Modals.vue";
import KTScrollTop from "@/layouts/main-layout/extras/ScrollTop.vue";
import KTCustomize from "@/layouts/main-layout/extras/Customize.vue";
import { useRoute } from "vue-router";
import { reinitializeComponents } from "@/core/plugins/keenthemes";
import LayoutService from "@/core/services/LayoutService";
import { initializeApp } from "firebase/app";
import { getDatabase, ref as dbRef, get } from "firebase/database";

export default defineComponent({
  name: "default-layout",
  components: {
    KTHeader,
    KTSidebar,
    KTContent,
    KTToolbar,
    KTFooter,
    KTDrawers,
    KTScrollTop,
    KTModals,
    KTCustomize,
  },
  setup() {
    const route = useRoute();

    onBeforeMount(() => {
      LayoutService.init();
    });

    onMounted(() => {
      nextTick(() => {
        reinitializeComponents();
      });
    });

    watch(
      () => route.path,
      () => {
        nextTick(() => {
          reinitializeComponents();
        });
      }
    );
  },
});
</script>
