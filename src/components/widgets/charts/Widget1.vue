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
      data: ''
    };
  },

  methods: {
    async parsedData() {
      try {
        return JSON.parse(this.data);
      } catch (error) {
        console.error("Error parsing data:", error);
        return null;
      }
    },
    async fetchData() {
      const firebaseConfig = {
        apiKey: "AIzaSyAzugDwrepHZdvszOSFyGRga3IEg1qV-3c",
        authDomain: "rfid-database-8c0f2.firebaseapp.com",
        databaseURL: "https://rfid-database-8c0f2-default-rtdb.asia-southeast1.firebasedatabase.app",
        projectId: "rfid-database-8c0f2",
        storageBucket: "rfid-database-8c0f2.appspot.com",
        messagingSenderId: "509413991821",
        appId: "1:509413991821:web:fef2ffd91ade42f62fc9c4",
        measurementId: "G-LMGX06N1SW"
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

        } else {
          console.log("No data found.");
        }
      } catch (error) {
        console.error("Error reading data:", error);
      }
    },
  },
  async mounted() {

  },
  setup() {

    const chartRef = ref<typeof VueApexCharts | null>(null);
    const chart = ref<ApexOptions>({});
    const store = useThemeStore();
    let fetchedData;


    const firebaseConfig = {
      apiKey: "AIzaSyAzugDwrepHZdvszOSFyGRga3IEg1qV-3c",
      authDomain: "rfid-database-8c0f2.firebaseapp.com",
      databaseURL: "https://rfid-database-8c0f2-default-rtdb.asia-southeast1.firebasedatabase.app",
      projectId: "rfid-database-8c0f2",
      storageBucket: "rfid-database-8c0f2.appspot.com",
      messagingSenderId: "509413991821",
      appId: "1:509413991821:web:fef2ffd91ade42f62fc9c4",
      measurementId: "G-LMGX06N1SW"
    };

    // Initialize Firebase
    const app = initializeApp(firebaseConfig);

    // Access the Realtime Database
    const db = getDatabase(app);
    const dataRef = dbRef(db, "RFID"); // Adjust the path as needed
    try {
      const snapshot = get(dataRef);
      if (snapshot.exists()) {
        // Data found
        fetchedData = snapshot.val(); //data in Object format you can call like below line
        console.log(fetchedData.Datetime);
      } else {
        console.log("No data found.");
      }
    } catch (error) {
      console.error("Error reading data:", error);
    }
    let data = JSON.stringify(fetchedData); //data in string format
    console.log(data);

    const series = [
      {
        name: "Net Profit",
        data: [44, 55, 57, 56, 61, 58],
      },
      {
        name: "Revenue",
        data: [76, 85, 101, 98, 87, 105],
      },
    ];

    const themeMode = computed(() => {
      return store.mode;
    });

    onBeforeMount(() => {
      Object.assign(chart.value, chartOptions());
    });

    const refreshChart = () => {
      if (!chartRef.value) {
        return;
      }

      chartRef.value.updateOptions(chartOptions());
    };

    watch(themeMode, () => {
      refreshChart();
    });

    return {
      chart,
      series,
      chartRef,
      getAssetPath,
    };
  },
});



const chartOptions = (): ApexOptions => {
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
      categories: ["Feb", "Mar", "Apr", "May", "Jun", "Jul"],
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
