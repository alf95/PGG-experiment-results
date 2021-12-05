<template>
  <div class="container">
      <h2>Payoffs PGG</h2>
      <form>
          <div class="form-row">
              <div class="form-group col-4">
                    <label for="my-input" class="col-form-label col-form-label-sm">Carica file in xlsx</label>
                    <input id="my-input" class="form-control-file" type="file" @change="readFile" @click="resetFile">
              </div>
              <div class="form-group col-4">
                    <label for="nRound" class="col-form-label col-form-label-sm mr-3">Inserisci il round</label>
                    <input type="number" class="form-control-sm" placeholder="Current round" id="nRound" v-model="nRound" @change="showResults = false">
              </div>
              <div class="form-group col-4" style="text-align: center;">
                    <label>&nbsp;</label>
                    <button class="btn btn-primary" type="button" @click="showPayoffs">Mostra Payoff</button>
              </div>
          </div>
        
        
        
        
      </form>
     
    <div v-show="showResults">
        <div class="row">
            <div class="col">
                <table class="table table-striped table-responsive ">
                    <thead>
                        <tr>
                            <th v-for="(value, colName) in columnsPositionMap" :key="value" v-show="colName == 'ID' || colName == 'Payoff ' + nRound">
                                {{colName.replace("ID", "Player")}}
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(infos, idPlayer) in playersDataMapOrdered" :key="idPlayer" v-show="idPlayer <= 12">
                            <td class="idPlayer">{{"X" + idPlayer}}</td>
                            <td v-for="(value, colPayoff) in infos" :key="colPayoff" :class="colPayoff" v-show="colPayoff == 'Payoff ' + nRound">{{value}}</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="col">
                <table class="table table-striped table-responsive ">
                    <thead>
                        <tr>
                            <th v-for="(value, colName) in columnsPositionMap" :key="value" v-show="colName == 'ID' || colName == 'Payoff ' + nRound">
                                {{colName.replace("ID", "Player")}}
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(infos, idPlayer) in playersDataMapOrdered" :key="idPlayer" v-show="idPlayer > 12 && idPlayer <= 24">
                            <td class="idPlayer">{{"X" + idPlayer}}</td>
                            <td v-for="(value, colPayoff) in infos" :key="colPayoff" :class="colPayoff" v-show="colPayoff == 'Payoff ' + nRound">{{value}}</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="col">
                <table class="table table-striped table-responsive ">
                    <thead>
                        <tr>
                            <th v-for="(value, colName) in columnsPositionMap" :key="value" v-show="colName == 'ID' || colName == 'Payoff ' + nRound">
                                {{colName.replace("ID", "Player")}}
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(infos, idPlayer) in playersDataMapOrdered" :key="idPlayer" v-show="idPlayer > 24">
                            <td class="idPlayer">{{"X" + idPlayer}}</td>
                            <td v-for="(value, colPayoff) in infos" :key="colPayoff" :class="colPayoff" v-show="colPayoff == 'Payoff ' + nRound">{{value}}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
        

        
    </div>
    
  </div>

</template>
<style>
    .idPlayer{
      font-weight: 600;
      color: red;
    }
</style>

<script>
import readXlsxFile from "read-excel-file";

export default {
  name: "TableResults",
  data() {
    return {
      columnsPositionMap: {
        ID: 0,
      },
      playersDataMap: {},
      playersDataMapOrdered:{},
      fileData: null,
      showResults:false,
      nRound:null
    };
  },
  created() {},
  methods: {
    readFile(event) {
      this.showResults = false;
      let self = this;
      this.fileData = event.target.files[0];
      let file = event.target.files[0];
      console.log(file);
      readXlsxFile(file).then((rows) => {
        console.log(rows);
        rows.shift();
        let columns = rows[0];
        columns.forEach((nameCol, index) => {
          if (nameCol && nameCol.toLowerCase().includes("payoff")) {
            self.columnsPositionMap[nameCol] = index;
          }
        });
        rows.forEach((row, index) => {
          if (index > 0) {
            let cells = rows[index];
            let idPlayer;
            let infoPlayer = {};
            if (cells[0]) {
              Object.entries(self.columnsPositionMap).forEach(([key, val]) => {
                let cellValue = cells[val];
                if (key == "ID") {
                  idPlayer = Number(cellValue.replace('X',''));
                } else {
                  infoPlayer[key] = cellValue;
                }
              });
              self.playersDataMap[idPlayer] = infoPlayer;
            }
          }
        });
        self.playersDataMapOrdered = Object.keys(self.playersDataMap).sort().reduce(
            (obj, key) => { 
                obj[key] = self.playersDataMap[key]; 
                return obj;
            }, 
            {}
        );
      });
    },
    resetFile(event){
        event.target.value = null;
    },
    showPayoffs(){
        this.showResults = true;
    }
  }
};
</script>