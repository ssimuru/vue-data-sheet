<template>
  <div id="app">
    <DataSheet :settings="settings"/>
  </div>
</template>

<script>
import DataSheet from './components/DataSheet.vue'

var testData = {
  gridData: [[1  , 'Seo', '36', '01077783642', 'Korea'],
[2  , 'John', '24', '01023458293', 'America'],
[3  , 'Smith', '48', '01047569283', 'Japan'],
[4  , 'Seo', '36', '01077783642', 'Korea'],
[5  , 'John', '24', '01023458293', 'America'],
[6  , 'Smith', '48', '01047569283', 'Japan'],
[7  , 'Seo', '36', '01077783642', 'Korea'],
[8  , 'John', '24', '01023458293', 'America'],
[9  , 'Smith', '48', '01047569283', 'Japan'],
[10 , 'Seo', '36', '01077783642', 'Korea'],
[11 , 'John', '24', '01023458293', 'America'],
[12 , 'Smith', '48', '01047569283', 'Japan'],
[13 , 'Seo', '36', '01077783642', 'Korea'],
[14 , 'John', '24', '01023458293', 'America'],
[15 , 'Smith', '48', '01047569283', 'Japan'],
[16 , 'Seo', '36', '01077783642', 'Korea'],
[17 , 'John', '24', '01023458293', 'America'],
[18 , 'Smith', '48', '01047569283', 'Japan'],
[19 , 'Seo', '36', '01077783642', 'Korea'],
[20 , 'John', '24', '01023458293', 'America'],
[21 , 'Smith', '48', '01047569283', 'Japan'],
[22 , 'Seo', '36', '01077783642', 'Korea'],
[23 , 'John', '24', '01023458293', 'America'],
[24 , 'Smith', '48', '01047569283', 'Japan'],
[25 , 'Seo', '36', '01077783642', 'Korea'],
[26 , 'John', '24', '01023458293', 'America'],
[27 , 'Smith', '48', '01047569283', 'Japan'],
[28 , 'Seo', '36', '01077783642', 'Korea'],
[29 , 'John', '24', '01023458293', 'America'],
[30 , 'Smith', '48', '01047569283', 'Japan'],
[31 , 'Seo', '36', '01077783642', 'Korea'],
[32 , 'John', '24', '01023458293', 'America'],
[33 , 'Smith', '48', '01047569283', 'Japan'],
[34 , 'Seo', '36', '01077783642', 'Korea'],
[35 , 'John', '24', '01023458293', 'America'],
[36 , 'Smith', '48', '01047569283', 'Japan'],
[37 , 'Seo', '36', '01077783642', 'Korea'],
[38 , 'John', '24', '01023458293', 'America'],
[39 , 'Smith', '48', '01047569283', 'Japan'],
[40 , 'Seo', '36', '01077783642', 'Korea'],
[41 , 'John', '24', '01023458293', 'America'],
[42 , 'Smith', '48', '01047569283', 'Japan'],
[43 , 'Seo', '36', '01077783642', 'Korea'],
[44 , 'John', '24', '01023458293', 'America'],
[45 , 'Smith', '48', '01047569283', 'Japan'],
[46 , 'Seo', '36', '01077783642', 'Korea'],
[47 , 'John', '24', '01023458293', 'America'],
[48 , 'Smith', '48', '01047569283', 'Japan'],
[49 , 'Seo', '36', '01077783642', 'Korea'],
[50 , 'John', '24', '01023458293', 'America'],
[51 , 'Smith', '48', '01047569283', 'Japan'],
[52 , 'Seo', '36', '01077783642', 'Korea'],
[53 , 'John', '24', '01023458293', 'America'],
[54 , 'Smith', '48', '01047569283', 'Japan'],
[55 , 'Seo', '36', '01077783642', 'Korea'],
[56 , 'John', '24', '01023458293', 'America'],
[57 , 'Smith', '48', '01047569283', 'Japan'],
[58 , 'Seo', '36', '01077783642', 'Korea'],
[59 , 'John', '24', '01023458293', 'America'],
[60 , 'Smith', '48', '01047569283', 'Japan'],
[61 , 'Seo', '36', '01077783642', 'Korea'],
[62 , 'John', '24', '01023458293', 'America'],
[63 , 'Smith', '48', '01047569283', 'Japan'],
[64 , 'Seo', '36', '01077783642', 'Korea'],
[65 , 'John', '24', '01023458293', 'America'],
[66 , 'Smith', '48', '01047569283', 'Japan'],
[67 , 'Seo', '36', '01077783642', 'Korea'],
[68 , 'John', '24', '01023458293', 'America'],
[69 , 'Smith', '48', '01047569283', 'Japan'],
[70 , 'Seo', '36', '01077783642', 'Korea'],
[71 , 'John', '24', '01023458293', 'America'],
[72 , 'Smith', '48', '01047569283', 'Japan'],
[73 , 'Seo', '36', '01077783642', 'Korea'],
[74 , 'John', '24', '01023458293', 'America'],
[75 , 'Smith', '48', '01047569283', 'Japan'],
[76 , 'Seo', '36', '01077783642', 'Korea'],
[77 , 'John', '24', '01023458293', 'America'],
[78 , 'Smith', '48', '01047569283', 'Japan']]
}

export default {
  name: 'app',
  components: {
    DataSheet
  },
  data() {
    return {
      settings: {
        name: 'data sheet!',
        rows: 3,
        cols: 5,
        width: '1000px',
        height: '500px',
        header: [
          {
            name: 'Number', 
            type: 'input'              
          },
          {
            name: 'Name', 
            type: 'input'              
          },
          {
            name: 'Age', 
            type: 'input'              
          },
          {
            name: 'Phone', 
            type: 'input'              
          },
          {
            name: 'Country', 
            type: 'input'              
          }
        ],  
        gridData: testData.gridData,
        type: 'default'
      }
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
</style>
