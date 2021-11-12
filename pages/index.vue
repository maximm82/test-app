<template>
  <v-container>
    <v-row>
      <v-col>
        <v-card width="400" class="mx-auto">
          <v-card-title>
            <span class="text-h5">Contract (Cake LP)</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col>
                  <v-text-field
                    label="Address Contract"
                    required
                    v-model="contractAddress"
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-btn class="mx-auto" color="blue" @click="consult()">
              Consult
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
      <v-col>
        <v-row>
          <v-card width="400">
            <v-card-title>
              <span class="text-h5">Info</span>
            </v-card-title>
            <v-card-text> Token1 : {{ token0.symbol }} </v-card-text>
            <v-card-text> Reserve : {{ token0.reserve }} </v-card-text>
            <v-card-text>
              Link :
              <v-btn color="blue" dark small fab :href="token0.link" target="_blank">
                <v-icon> mdi-link </v-icon>
              </v-btn>
            </v-card-text>
          </v-card>
        </v-row>
        <br />
        <br />
        <v-row>
          <v-card width="400">
            <v-card-title>
              <span class="text-h5">Info</span>
            </v-card-title>
            <v-card-text> Token2 : {{ token1.symbol }} </v-card-text>
            <v-card-text> Reserve : {{ token1.reserve }} </v-card-text>
            <v-card-text>
              Link :
              <v-btn color="blue" dark small fab :href="token1.link" target="_blank">
                <v-icon> mdi-link </v-icon>
              </v-btn>
            </v-card-text>
          </v-card>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const Web3 = require("web3");
const abi = require("../static/ABIS/LPCakeABI.json");
const abiERC20 = require("../static/ABIS/ERC20.json");
//const contractAddress = "0xAd2202B9E4b4CFb4d3505945B00be9c3Ea1dc260";
var web3 = new Web3(Web3.givenProvider);
//const contract = new web3.eth.Contract(abi.default, contractAddress);
export default {
  data: () => ({
    to: "",
    marketcap: 0,
    contractAddress: "",
    token0: {
      symbol: "",
      reserve: 0,
      link: "",
    },
    token1: {
      symbol: "",
      reserve: 0,
      link: "",
    },
  }),
  methods: {
    async consult() {
      try {
        var contract = new web3.eth.Contract(
          abi,
          this.contractAddress
        );
        //var userAccount = web3.currentProvider.selectedAddress;
        let info = await contract.methods.getReserves().call();
        this.token0.reserve =  web3.utils.fromWei(info._reserve0);
        this.token1.reserve = web3.utils.fromWei(info._reserve1);
        
        let token0 = await contract.methods.token0().call();
        let token1 = await contract.methods.token1().call();
        var token0Contract = new web3.eth.Contract(
          abiERC20,
          token0)
        var token1Contract = new web3.eth.Contract(
          abiERC20,
          token1)
        this.token0.link =  "https://bscscan.com/token/"+token0
        this.token1.link = "https://bscscan.com/token/"+token1
        this.token0.symbol = await token0Contract.methods.symbol().call();
        this.token1.symbol = await token1Contract.methods.symbol().call();
        console.log(info);
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style>
</style>
