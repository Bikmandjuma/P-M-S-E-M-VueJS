<template>
  <!-- Content -->
  <div class="mt-2">

    <div class="grid grid-cols-1 gap-8 p-4 lg:grid-cols-2 xl:grid-cols-4">
      <!-- Temperature card -->
      <div class="flex"></div>
      <div class="flex items-center justify-between p-4 bg-white rounded-md dark:bg-darker">
        <div>
          <h6 class="text-xl font-medium leading-none tracking-wider text-gray-500 uppercase dark:text-primary-light">
            Temperature
          </h6>
          <span class="text-md font-semibold">{{ dataInputs.temperature }} °C</span>
        </div>
        <div>
          <span class="fa fa-list-alt w-11 h-11 text-gray-300 dark:text-primary-dark"></span>
        </div>
      </div>

      <!-- Vibration card -->
      <div class="flex items-center justify-between p-4 bg-white rounded-md dark:bg-darker">
        <div>
          <h6 class="text-md font-medium leading-none tracking-wider text-gray-500 uppercase dark:text-primary-light">
            Vibration
          </h6>
          <span class="text-xl font-semibold">{{ dataInputs.vibration }} m/s²</span>
        </div>
        <div>
          <span class="fa fa-list-alt w-11 h-11 text-gray-300 dark:text-primary-dark"></span>
        </div>
      </div>

      <div class="flex"></div>

    </div>

    <!-- Crop Prediction Result -->
    <div class="flex flex-wrap mt-3">
      <div class="w-full xl:w-2/2 mb-4">
        <div class="card border rounded-lg shadow-md">
          <div class="card-body pt-3 text-center" style="overflow-x: scroll;">
            <h3 v-if="isLoading" class="text-blue-600 font-bold">Loading...</h3>

            <template v-if="!isLoading">
              <div class="text-center">
                <h2 class="flex items-center justify-center gap-2">
                  <i class="fas fa-motorcycle text-gray-500"></i>
                  Motorcycle State
                </h2>
                <hr class="my-2" />

                <h3 v-if="!isInvalid" class="flex items-center justify-center gap-2">
                  <i :class="stateIconClass"></i>
                  State:
                  <span class="font-bold text-blue-600">
                    <!-- {{ prediction.most_probable_state }} -->
                    {{ prediction.most_probable_state?.trim() || 'No state' }}
                  </span>
                  
                </h3>

                <p v-if="!isInvalid" class="text-gray-500 flex justify-center items-center gap-2">
                  <i class="fas fa-bullseye text-indigo-500"></i>
                  Confidence: {{ (prediction.predicted_probability * 100).toFixed(2) }}%
                </p>

                <p v-if="!isInvalid" class="text-gray-500 flex justify-center items-center gap-2">
                  <i class="fas fa-percentage text-purple-500"></i>
                  Model accuracy: {{ prediction.model_accuracy }}%
                </p>

                <h3 v-if="isInvalid" class="text-red-600 font-bold flex justify-center items-center gap-2">
                  <i class="fas fa-ban text-red-600"></i>
                  No state is found for the given conditions.
                </h3>
              </div>
            </template>

          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import { flaskApiUrl, laravelApiUrl } from "../../api";

export default {
  name: "ManagerDashboard",
  data() {
    return {
      isLoading: true,
      isInvalid: false,
      hasSentWarningEmail: false, // Track if the email has been sent
      dataInputs: {
        temperature: 0,
        vibration: 0,
      },
      prediction: {
        model_accuracy: 0,
        most_probable_state: "no state",
        predicted_probability: 0
      }
    };
  },
  computed: {
    stateIconClass() {
      const state = this.prediction.most_probable_state?.trim().toLowerCase();

      switch(state) {
        case 'warning':
          return 'fas fa-exclamation-triangle text-yellow-500';
        case 'healthy':
          return 'fas fa-check-circle text-green-500';
        case 'failing':
          return 'fas fa-times-circle text-red-600';
        default:
          return 'fas fa-question-circle text-gray-400';
      }
    }

  },
  methods: {
    async fetchDataInputs() {
      try {
        const response = await fetch(`${laravelApiUrl}/getting-Data`);
        const data = await response.json();
        this.dataInputs = data;
        this.checkInvalidConditions();
      } catch (error) {
        console.error("Error fetching crop inputs:", error);
      }
    },

    async fetchCropPrediction() {
      try {
        // Fetch prediction data from the Flask API
        const response = await fetch(`${flaskApiUrl}/state`);
        const data = await response.json();

        // Update prediction object with the fetched data
        this.prediction = {
          model_accuracy: data.model_accuracy,
          most_probable_state: data.most_probable_state,
          predicted_probability: data.predicted_probability,
        };

        // Check for invalid conditions based on fetched data
        this.checkInvalidConditions();

        // If the state is "Warning" and email has not been sent yet, send it
        if (this.prediction.most_probable_state === "Warning" && !this.hasSentWarningEmail) {
          this.triggerWarningEmail();
          this.hasSentWarningEmail = true; // Mark email as sent
        }

        // Reset the flag when the state is back to normal
        if (this.prediction.most_probable_state !== "Warning") {
          this.hasSentWarningEmail = false;
        }

      } catch (error) {
        console.error("Error fetching crop prediction:", error);
      }
    },

    async triggerWarningEmail() {
      try {
        const token = localStorage.getItem("auth_token");

        const response = await fetch(`${laravelApiUrl}/send-warning-email`, {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            Authorization: `Bearer ${token}`
          },
          body: JSON.stringify({ state: "Warning" })
        });

        if (response.ok) {
          console.log("Warning email sent successfully.");
        } else {
          console.error("Failed to send warning email.");
        }
      } catch (error) {
        console.error("Error sending warning email:", error);
      }
    },

    checkInvalidConditions() {
      const { model_accuracy, predicted_probability } = this.prediction;

      this.isInvalid = model_accuracy === 0 || predicted_probability <= 0;

      if (this.isInvalid) {
        this.prediction = {
          model_accuracy: 0,
          most_probable_state: "No recommendation",
          predicted_probability: 0
        };
      }
    }
  },
  mounted() {
    // Fetch crop prediction and data inputs immediately after the component is mounted
    this.fetchCropPrediction();
    this.fetchDataInputs();

    setTimeout(() => {
      this.isLoading = false;  // Hide loading after 3 seconds
    }, 3000);

    // Periodically fetch the crop prediction data every 5 seconds
    setInterval(() => {
      this.fetchCropPrediction();
    }, 5000);

    // Periodically fetch the temperature and vibration data every 5 seconds
    setInterval(() => {
      this.fetchDataInputs();
    }, 5000);
  }
};
</script>
