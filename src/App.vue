<template>
  <div class="h-screen flex items-center flex-col">
    <img class="pt-5" src="./assets/cropped-logo.png" alt="" />

    <p v-if="this.accounts.length > 0" class="text-green-500 font-bold">
      {{ this.trimAddress(this.accounts[0]) }}
    </p>
    <p v-else class="text-red-500 font-bold">NO ACCOUNT CONNECTED</p>

    <div class="mint-container flex flex-col items-center justify-center">
      <p class="title mb-3 text-center">
        READY TO TAKE OVER THE METAVERSE AND THE NFT SPACE
      </p>
      <p class="subtitle mb-3 text-center">
        LIMITED SALE IS NOW <span class="subtitle-color">LIVE</span>
      </p>
      <div>
        <span class="amountSelector minus" @click="this.deleteOne()">-</span>
        <span class="amount">{{ this.amount }}</span>
        <span class="amountSelector plus" @click="this.addOne()">+</span>
      </div>
      <p class="mb-3">
        TOTAL PRICE : <span>{{ this.costAmount.toFixed(1) }}</span> ETH
      </p>
      <button class="button-mint mb-3" @click="this.mint()">MINT</button>
    </div>

    <div>test</div>
  </div>
</template>

<script>
import { ethers } from "ethers";

export default {
  name: "App",
  components: {},
  data() {
    return {
      amount: 1,
      cost: 0.2,
      accounts: [],
      destAddress: "0x4C8885667818F689E268e59e234d95D2F2E639b2",
    };
  },

  async mounted() {
    // ajout event

    window.ethereum.on("accountsChanged", () => {
      // change de compte
      window.location.reload();
    });

    window.ethereum.on("chainChanged", () => {
      // change de réseau
      window.location.reload();
    });

    window.ethereum.on("disconnect", () => {
      // se deconnecte
      window.location.reload();
    });

    this.getAccount();
  },

  computed: {
    costAmount() {
      return this.amount * this.cost;
    },
  },

  methods: {
    trimAddress(ad) {
      return ad.substring(0, 5) + "..." + ad.substring(ad.length - 4);
    },

    async getAccount() {
      if (typeof window.ethereum != "undefined") {
        this.accounts = await window.ethereum.request({
          method: "eth_requestAccounts",
        });
      }
    },

    addOne() {
      if (this.amount < 5) {
        this.amount++;
      }
    },

    deleteOne() {
      if (this.amount > 1) {
        this.amount--;
      }
    },

    mint() {
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner();

      let tx = {
        from: this.accounts[0],
        to: this.destAddress,
        value: ethers.utils.parseEther(this.costAmount.toString()),
      };

      signer.sendTransaction(tx);
    },
  },
};
</script>
