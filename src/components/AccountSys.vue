<template>
  <div>
    <h1>助记词</h1>
    <p>
      {{ mnemonic }}
    </p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Web3 from 'web3'
import Tx from 'ethereumjs-tx'
import * as bip39 from 'bip39'
import { hdkey } from 'ethereumjs-wallet'



// const mnemonic = bip39.generateMnemonic()
// console.log(mnemonic)

const mnemonic = ref("course involve token inch frame iron problem common slow clip wedding salmon")

const getMnemonicSeed = async () => {
  const seed = await bip39.mnemonicToSeed(mnemonic.value)
  const hdWallet = hdkey.fromMasterSeed(seed)
  const keypair = hdWallet.derivePath("m/44'/60'/0'/0/0")
  console.log(keypair)
}

getMnemonicSeed()
</script>

<style lang="less">
body {
  padding: 50px;
}
</style>
