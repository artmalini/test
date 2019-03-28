<template>
  <div id="app">
    <table class="table table-striped table-dark" v-if="!err">
      <thead>
        <tr>
          <th scope="col" colspan="2" class="frontier">Coin</th>
          <th scope="col">Price</th>
          <th scope="col">Supply</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="currency in info" :key="currency.id">
          <td class="sempl"><img class="curenimg" :src="vals+currency.CoinInfo.ImageUrl" alt="" /></td>
          <td class="frontier"><span>{{ currency.CoinInfo.FullName }}</span><br>{{ currency.CoinInfo.Name }}</td>
          <td>{{ currency.DISPLAY.USD.PRICE }}</td>
          <td>{{ currency.DISPLAY.USD.SUPPLY }}</td>
        </tr>
      </tbody>
    </table>
    <div class="alert alert-danger" role="alert" v-if="err == 1">
      {{ msg }}
    </div>  
  </div>
</template>

<script>


function convert(resp) {
    for (var i = 0; i < resp.length; i++) {
        for (var j = 0; j < resp.length - 1; j++) {
            if (resp[j].RAW.USD.PRICE < resp[j + 1].RAW.USD.PRICE) {
                let tmp = resp[j];
                resp[j] = resp[j + 1];
                resp[j + 1] = tmp;
            }
        }
    }
    return resp;
}

export default {
  name: 'app',
  data() {
     return  {
        info: null,
        imterval: null,
        vals: 'https://www.cryptocompare.com',
        start: 0,
        err: 0,
        msg: null,
     }
  },
  mounted() {
    this.getReq();
    this.interval = setInterval(this.getReq, 300000);
  },
  methods: {
    getReq: function() {
      this.$axios
       .get('https://min-api.cryptocompare.com/data/top/mktcapfull?limit=20&tsym=USD')
       .then(response => {
          this.err = 0;
          this.info = convert(response.data.Data);
          if (response.data.Type == 2) {
            this.err = 1;
            this.msg = response.data.Message;
            clearInterval(this.interval);
          }      
       })
       .catch(error => {
           this.err = 1;
           this.msg = error;
           clearInterval(this.interval);
        })
    }
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#app table {
    width: 800px;
    margin: 0 auto;
}
#app .sempl {
    vertical-align: middle;
}
#app .curenimg {
    width: 53px;
}
#app .frontier {
    border-right: 1px solid #65615b;
}
</style>
