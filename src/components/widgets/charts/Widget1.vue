<template>
  <!--begin::Charts Widget 1-->
  <div class="card" :class="widgetClasses">
    <!--begin::Header-->
    <div class="card-header border-0 pt-5">
      <!--begin::Title-->
      <h3 class="card-title align-items-start flex-column">
        <span class="card-label fw-bold fs-3 mb-1">Recent Statistics</span>
        <span class="text-muted fw-semobold fs-7">More than 400 new members</span>
      </h3>
      <!--end::Title-->

      <!--begin::Toolbar-->
      <div class="card-toolbar">
        <!--begin::Menu-->
        <button type="button" class="btn btn-sm btn-icon btn-color-primary btn-active-light-primary"
          data-kt-menu-trigger="click" data-kt-menu-placement="bottom-end" data-kt-menu-flip="top-end">
          <KTIcon icon-name="category" icon-class="fs-2" />
        </button>
        <Dropdown1></Dropdown1>
        <!--end::Menu-->
      </div>
      <!--end::Toolbar-->
    </div>
    <!--end::Header-->

    <!--begin::Body-->
    <div class="card-body">
      <!--begin::Chart-->
      <apexchart ref="chartRef" type="bar" :options="chart" :series="series" :height="height"></apexchart>
      <!--end::Chart-->
    </div>
    <!--end::Body-->
  </div>
  <!--end::Charts Widget 1-->
</template>

<script lang="ts">
import { getAssetPath } from "@/core/helpers/assets";
import { computed, defineComponent, onBeforeMount, ref, watch } from "vue";
import { useThemeStore } from "@/stores/theme";
import type { ApexOptions } from "apexcharts";
import Dropdown1 from "@/components/dropdown/Dropdown1.vue";
import { getCSSVariableValue } from "@/assets/ts/_utils";
import type VueApexCharts from "vue3-apexcharts";
import { initializeApp } from "firebase/app";
import { getDatabase, ref as dbRef, get } from "firebase/database";

export default defineComponent({
  name: "widget-1",
  props: {
    widgetClasses: String,
    height: Number,
  },
  components: {
    Dropdown1,
  },
  data() {
    return {
      fetchedData: null,
      data: "",
      categories: [] as string[],
      chart: ref<ApexOptions>({}),
      series: [] as any[], // Use an array for series data
    };
  },
  computed: {
    parsedData() {
      try {
        return JSON.parse(this.data);
      } catch (error) {
        console.error("Error parsing data:", error);
        return null;
      }
    },
  },
  methods: {
    async fetchData() {
      const firebaseConfig = {
        // ... (firebaseConfig, initializeApp, and dataRef code)
      };

      // Initialize Firebase
      const app = initializeApp(firebaseConfig);

      // Access the Realtime Database
      const db = getDatabase(app);
      const dataRef = dbRef(db, "RFID"); // Adjust the path as needed
      try {
        const snapshot = await get(dataRef);
        if (snapshot.exists()) {
          // Data found
          this.fetchedData = snapshot.val(); // Store data in the component's data property
          this.categories = Object.keys(this.parsedData.Datetime);

          // Update series data
          this.series = this.categories.map((category) => ({
            name: category,
            data: this.parsedData.Datetime[category],
          }));
        } else {
          console.log("No data found.");
        }
      } catch (error) {
        console.error("Error reading data:", error);
      }
    },
  },
  async mounted() {
    await this.fetchData();
    this.data = JSON.stringify(this.fetchedData);

    // Automatically refresh data every 30 seconds
    setInterval(async () => {
      await this.fetchData();
    }, 5000); // 30 seconds in milliseconds

    const store = useThemeStore();

    onBeforeMount(() => {
      // Update chart options directly
      const options = chartOptions(this.categories);
      if (this.$refs.chartRef) {
        this.$refs.chartRef.updateOptions(options);
      }
    });

    const refreshChart = () => {
      if (this.$refs.chartRef) {
        this.$refs.chartRef.updateOptions(chartOptions(this.categories));
      }
    };

    const themeMode = computed(() => {
      return store.mode;
    });

    watch(themeMode, () => {
      refreshChart();
    });
  },
  setup() {
    return {
      getAssetPath,
    };
  },
});

const chartOptions = (categories: string[]): ApexOptions => {
  const labelColor = getCSSVariableValue("--bs-gray-500");
  const borderColor = getCSSVariableValue("--bs-gray-200");
  const baseColor = getCSSVariableValue("--bs-primary");
  const secondaryColor = getCSSVariableValue("--bs-gray-300");

  return {
    chart: {
      fontFamily: "inherit",
      type: "bar",
      toolbar: {
        show: false,
      },
    },
    plotOptions: {
      bar: {
        horizontal: false,
        columnWidth: "30%",
        borderRadius: 5,
      },
    },
    legend: {
      show: false,
    },
    dataLabels: {
      enabled: false,
    },
    stroke: {
      show: true,
      width: 2,
      colors: ["transparent"],
    },
    xaxis: {
      categories: categories,
      axisBorder: {
        show: false,
      },
      axisTicks: {
        show: false,
      },
      labels: {
        style: {
          colors: labelColor,
          fontSize: "12px",
        },
      },
    },
    yaxis: {
      labels: {
        style: {
          colors: labelColor,
          fontSize: "12px",
        },
      },
    },
    fill: {
      opacity: 1,
    },
    states: {
      normal: {
        filter: {
          type: "none",
          value: 0,
        },
      },
      hover: {
        filter: {
          type: "none",
          value: 0,
        },
      },
      active: {
        allowMultipleDataPointsSelection: false,
        filter: {
          type: "none",
          value: 0,
        },
      },
    },
    tooltip: {
      style: {
        fontSize: "12px",
      },
      y: {
        formatter: function (val) {
          return "$" + val + " thousands";
        },
      },
    },
    colors: [baseColor, secondaryColor],
    grid: {
      borderColor: borderColor,
      strokeDashArray: 4,
      yaxis: {
        lines: {
          show: true,
        },
      },
    },
  };
};
</script>
