<template>
  <div>
    <p>地址:{{ address }}</p>
    <p>私钥:{{ privateKey }}</p>

    <van-button type="primary" @click="send">转账</van-button>
    <p>余额:{{ myMoney }}</p>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import Web3 from 'web3'
import Tx from 'ethereumjs-tx'


var web3 = new Web3(Web3.givenProvider || 'wss://sepolia.infura.io/ws/v3/b1e34e60d9054ec89b394207eaf3d107');
// const user1 = web3.eth.accounts.create()
// console.log(user1)
const address = ref("0x01c47E2123f04962102b9B6CaBa89261d197D927")
const privateKey = ref("0x968d680597db6de3742a04ba36751a1cabf7a709a7544519bc37854572753779")
const myMoney = ref("0")

web3.eth.getBalance(address.value).then((ret) => {
  console.log(ret)
  myMoney.value = web3.utils.fromWei(ret, "ether")
});

const send = async () => {
  const nonce = await web3.eth.getTransactionCount(address.value)
  console.log(nonce)
  //获取预计燃料费
  const gasPrice = await web3.eth.getGasPrice()
  //转账金额
  const sendPrice = web3.utils.toWei("0.01", "ether")
  console.log(sendPrice)
  const rawTx = {
    from: address.value,
    to: "0xFEA562BE14b84FD23CA94cC4615A22A71d22B96D",
    nonce,
    gasPrice,
    value: sendPrice,
    data: "0x0000",
  }

  //转私钥buff
  const pkbuff = Buffer.from(privateKey.value.slice(2), 'hex')
  //gas 估算
  const gas = await web3.eth.estimateGas(rawTx)
  rawTx.gas = gas
  const tx = new Tx(rawTx, { 'chain': 'mainnet' })
  tx.sign(pkbuff)
  // const serializeTx = tx.serialize()
  const serializeTx = `0x${tx.serialize().toString("hex")}`
  console.log(serializeTx)

  //开始转账
  const trans = web3.eth.sendSignedTransaction(serializeTx)
  trans.on("transactionHash", (txid) => {
    console.log("必易ID:", txid);
    console.log(`https://sepolia.etherscan.io/tx/${txid}`);
  }).on('receipt', function (receipt) {
    console.log("第一个节点确认:", receipt);
  }).on("confirmation", (confirmationNumber, receipt) => {
    console.log("第n个节点碎认");
  });
}

</script>

<style lang="less">
body {
  padding: 50px;
}
</style>
