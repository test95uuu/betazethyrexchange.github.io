<template>
    <div class="user-page">
         <b-container class="bv-example-row">
          <b-row>
                <b-col lg="2" md="12">
                    <AccountMenu />
                </b-col>
                <b-col lg="10" md="12">
                    <ZtrProcess />
                </b-col>
          </b-row>
      </b-container>       
    </div>
</template>

<script>
import AccountMenu from'./../../components/MyAccount/AccountMenu/Account-Menu.vue'
import ZtrProcess from'./../../components/MyAccount/AccountContent/ZtrProcess.vue'
export default { 
  components:{
    AccountMenu,
    ZtrProcess
  }
}
</script>

<style lang="scss">

</style>