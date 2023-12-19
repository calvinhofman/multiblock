<template>
  <div class="container mx-auto mt-40">
    <div class="flex flex-row items-center space-x-16 justify-center">
      <div class="lg:w-9/12 xl:w-5/12 flex flex-col">
        <div>
          <h2 class="text-white text-3xl font-bold">Swap</h2>
          <p class="text-white">anonymous, secure, KYC-free</p>
        </div>
        <div class="flex flex-row justify-between">
          <div class="flex items-center space-x-4 mt-4">
            <p class="text-white">Private</p>
            <button class="relative h-6 w-12 bg-gray-400 rounded-full focus:outline-none" @click="toggleSwitch">
              <!-- Switch Handle -->
              <div :class="{ 'translate-x-full': !toggleValue }"
                class="absolute h-4 w-5 bg-white rounded-full top-1 left-1 transition-transform duration-300 ease-in-out">
              </div>
            </button>
            <p class="text-white">quick swap</p>

          </div>
          <div class="flex items-center space-x-4 mt-4">
            <p class="text-white">Variable</p>
            <button class="relative h-6 w-12 bg-gray-400 rounded-full focus:outline-none" @click="toggleSwitch1">
              <!-- Switch Handle -->
              <div :class="{ 'translate-x-full': toggleValue1 }"
                class="absolute h-4 w-5 bg-white rounded-full top-1 left-1 transition-transform duration-300 ease-in-out">
              </div>
            </button>
            <p class="text-white">Exact</p>

          </div>
        </div>
        <!-- First Dropdown -->
        <div class="bg-[#132044] rounded-2xl p-4 mt-4 flex flex-row justify-between text-white relative">
          <div class="flex flex-row w-full justify-between">
            <div class="flex flex-col space-y-4">
              <p>Send:</p>
              <!-- <input class="bg-transparent" placeholder="amount" type="text" name="amountInput" id="amountInput"
                v-model="amount" /> -->
              <input class="bg-transparent" placeholder="amount" type="number" name="amountInput" id="amountInput"
                :value="apiAmountIn" @input="handleInput" />
            </div>
            <div class="flex flex-col space-y-4 relative z-10">
              <div class="flex justify-end relative">
                <div @click="toggleFirstDropdown" class="custom-dropdown cursor-pointer pr-8">
                  <div class="text-white bg-transparent w-12/12 flex flex-row items-center space-x-2">
                    <img class="w-8 h-8" :src="selectedToken ? selectedToken.icon : ''" alt="" srcset="">

                    <div class="">
                      {{ selectedToken ? selectedToken.symbol + ' (' + selectedToken.network.shortName + ')' :
                        'SelectToken' }}
                    </div>
                  </div>
                </div>

              </div>
              <!-- <p>1 ETH = 2300 USDT (ERC-20)</p> -->
            </div>
          </div>
          <div v-if="isDropdownOpen" class="dropdown-menu">
            <div v-for="token in tokens" :key="token.address" @click="selectToken(token)"
              class="cursor-pointer px-4 py-2 hover:bg-gray-200 border-b border-gray-400 text-black">
              {{ token.symbol }} ({{ token.network.shortName }})
            </div>
          </div>
        </div>
        <!-- Second Dropdown -->
        <div class="bg-[#132044] rounded-2xl p-4 mt-4 flex flex-row justify-between text-white relative">
          <div class="flex flex-row w-full justify-between">
            <div class="flex flex-col space-y-4">
              <p>Receive:</p>
              <div class="" v-if="toggleValue1 == true">
                <input class=" bg-transparent" placeholder="amount" type="text" name="apiAmountInput" id="apiAmountInput"
                  :value="apiAmountOut" @input="handleInputOut">
              </div>
              <div class="" v-else>
                <input class="bg-transparent border-none" placeholder="amount" type="text" name="apiAmountInput"
                  id="apiAmountInput" :value="apiAmountOut" disabled />

              </div>
            </div>
            <div class="flex flex-col space-y-4">
              <div class="flex justify-end relative">
                <div @click="toggleSecondDropdown" class="custom-dropdown cursor-pointer pr-8">
                  <div class="text-white bg-transparent w-12/12 flex flex-row items-center space-x-2">
                    <img class="w-8 h-8" :src="selectedToken2 ? selectedToken2.icon : ''" alt="" srcset="">
                    <div class="">
                      {{ selectedToken2 ? selectedToken2.symbol + ' (' + selectedToken2.network.shortName + ')' :
                        'SelectToken' }}
                    </div>
                  </div>
                </div>

              </div>
              <!-- <p>1 ETH = 2300 USDT (ERC-20)</p> -->
            </div>
          </div>
          <div v-if="isSecondDropdownOpen" class="dropdown-menu">
            <div v-for="token in tokens" :key="token.address + '2'" @click="selectToken2(token)"
              class="cursor-pointer px-4 py-2 hover:bg-gray-200 border-b border-gray-400 text-black">
              {{ token.symbol }} ({{ token.network.shortName }})
            </div>
          </div>
        </div>
        <!-- Additional Input -->
        <div class="bg-[#132044] rounded-2xl p-4 mt-4 flex flex-row justify-between text-white">
          <div class="flex flex-row w-full justify-between">
            <div class="flex flex-col space-y-4">
              <p>Receiving Wallet Address:</p>
              <input class=" w-[25rem] bg-transparent" placeholder="Receiving Wallet Address:" type="text"
                name="amoutInput" id="" :value="address" @input="handleaddress">
            </div>
          </div>
        </div>
        <p class="my-4 text-white text-xs text-center">*Only send from wallets. Transactions sent from a Smart Contract
          are not accepted.</p>
        <button class="w-full rounded-full py-4 bg-white font-bold" @click="swap">SWAP NOW</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      tokens: [],
      selectedToken: null,
      selectedToken2: null,
      isDropdownOpen: false,
      isSecondDropdownOpen: false,
      toggleValue: true, // Add a toggle value in the data
      toggleValue1: false, // Add a toggle value in the data
      amount: '', // Added input for amount
      apiAmountIn: '',
      apiAmountOut: '',
      address: '',
    };
  },
  mounted() {
    // Make the API call when the component is mounted
    this.fetchTokens();
  },
  watch: {
    apiAmountIn(newapiAmountIn) {
      // Watch for changes in the amount property
      console.log('kekwe')
      this.apiAmountIn = newapiAmountIn
    },
  },
  methods: {
    handleInput(event) {
      this.apiAmountIn = event.target.value;

      this.makeApiCall(this.apiAmountIn);
    },

    handleaddress(event) {
      this.address = event.target.value;
    },

    handleInputOut(event) {
      this.apiAmountOut = event.target.value;

      this.makeApiCall1(this.apiAmountOut);
    },
    async fetchTokens() {
      try {
        const corsAnywhereUrl = 'https://cors-anywhere.herokuapp.com/';
        const apiEndpoint = 'https://api-partner.houdiniswap.com/tokens';
        const proxyApiEndpoint = `${corsAnywhereUrl}${apiEndpoint}`;

        // Make the API call with headers inside an object
        const response = await axios.get(proxyApiEndpoint, {
          headers: {
            'Authorization': '6578f49159f6f6f03047e689:9xzZaQuxqA1kznYcCVLTms',
          },
        });
        // Update the tokens array with the response data
        this.tokens = response.data;
        console.log(this.tokens)
        this.selectedToken = this.tokens[1]
        this.selectedToken2 = this.tokens[2];

      } catch (error) {
        console.error('Error fetching tokens:', error);
      }
    },
    toggleFirstDropdown() {
      this.isDropdownOpen = !this.isDropdownOpen;
      this.isSecondDropdownOpen = false;
    },
    toggleSecondDropdown() {
      this.isSecondDropdownOpen = !this.isSecondDropdownOpen;
      this.isDropdownOpen = false;

    },
    selectToken(token) {
      this.selectedToken = token;
      this.isDropdownOpen = false;
    },
    selectToken2(token) {
      this.selectedToken2 = token;
      this.isSecondDropdownOpen = false;
    }, toggleSwitch() {
      this.toggleValue = !this.toggleValue;
    },
    toggleSwitch1() {
      this.toggleValue1 = !this.toggleValue1;
      console.log(this.toggleValue1)
    },
    makeApiCall(param) {
      const amount = param
      console.log(amount)
      console.log(this.apiAmountIn)
      console.log(this.selectedToken)
      console.log(this.selectedToken2)

      if (!this.selectedToken || !this.selectedToken2 || !amount) {
        console.error('Please select tokens and provide an amount.');
        return;
      }
      const corsAnywhereUrl = 'https://cors-anywhere.herokuapp.com/';
      const apiEndpoint = 'https://api-partner.houdiniswap.com/quote';
      const proxyApiEndpoint = `${corsAnywhereUrl}${apiEndpoint}`;


      const direction = 'from'; // You mentioned direction as 'from' in the example
      const headers = {
        'Authorization': '6578f49159f6f6f03047e689:9xzZaQuxqA1kznYcCVLTms',
      };
      const params = {
        amount,
        from: this.selectedToken.symbol,
        to: this.selectedToken2.symbol,
        anonymous: this.toggleValue,
        fixed: this.toggleValue1,
        direction,
      };

      console.log(amount)

      // Make the API call
      axios.get(proxyApiEndpoint, { params, headers }).then((response) => {
        // Handle the API response as needed
        console.log('API Response:', response.data);
        this.apiAmountOut = response.data.amountOut
      })
        .catch((error) => {
          console.error('Error making API call:', error);
        });
    },

    makeApiCall1(param) {
      const amount = param
      console.log(amount)
      console.log(this.apiAmountIn)
      console.log(this.selectedToken)
      console.log(this.selectedToken2)

      if (!this.selectedToken || !this.selectedToken2 || !amount) {
        console.error('Please select tokens and provide an amount.');
        return;
      }
      const corsAnywhereUrl = 'https://cors-anywhere.herokuapp.com/';
      const apiEndpoint = 'https://api-partner.houdiniswap.com/quote';
      const proxyApiEndpoint = `${corsAnywhereUrl}${apiEndpoint}`;


      const direction = 'from'; // You mentioned direction as 'from' in the example
      const headers = {
        'Authorization': '6578f49159f6f6f03047e689:9xzZaQuxqA1kznYcCVLTms',
      };
      const amounts = parseInt(this.apiAmountIn);
      const params = {
        amount: amounts,
        from: this.selectedToken2.symbol,
        to: this.selectedToken.symbol,
        anonymous: this.toggleValue,
        fixed: this.toggleValue1,
        direction,
      };

      console.log(amount)

      // Make the API call
      axios.get(proxyApiEndpoint, { params, headers }).then((response) => {
        // Handle the API response as needed
        console.log('API Response:', response.data);
        this.apiAmountIn = response.data.amountOut
      })
        .catch((error) => {
          console.error('Error making API call:', error);
        });
    },

    async swap() {
      try {
        // Your existing API endpoint and headers
        const corsAnywhereUrl = 'https://cors-anywhere.herokuapp.com/';
        const apiEndpoint = 'https://api-partner.houdiniswap.com/exchange';
        const proxyApiEndpoint = `${corsAnywhereUrl}${apiEndpoint}`;
        const headers = {
          'Authorization': '6578f49159f6f6f03047e689:9xzZaQuxqA1kznYcCVLTms',
        };

        // Your existing params and additional params for swapping
        const amount = parseFloat(this.apiAmountIn)
        const params = {
          amount: amount,
          from: this.selectedToken.symbol,
          to: this.selectedToken2.symbol,
          addressTo: this.address, // Use the selected address
          anonymous: this.toggleValue,
          fixed: this.toggleValue1,
          direction: 'from', // You mentioned direction as 'from' in the example
        };

        console.log(params)
        // Make the API call
        const response = await axios.post(proxyApiEndpoint, null, { params, headers });

        // Handle the API response as needed
        console.log('Swap API Response:', response.data);

        // Optionally, you can reset the selectedToken, selectedToken2, and apiAmountIn values
        this.selectedToken = null;
        this.selectedToken2 = null;
        this.apiAmountIn = '';

      } catch (error) {
        console.error('Error during swap:', error);
        // Handle errors as needed
      }
    },
  },
};
</script>

<style scoped>
.dropdown-menu {
  position: absolute;
  z-index: 20;
  top: 100%;
  left: 0;
  margin-top: 2px;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  width: 100%;
  overflow-y: scroll;
  max-height: 400px;
}
</style>
