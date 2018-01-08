<template>
  <div>
    <Search></Search>
    <TabList></TabList>
  </div>
</template>

<script>
import axios from 'axios';
import Search from './Search';
import TabList from './TabList';
export default {
  name: 'HelloWorld',
  components:{
    Search,TabList
  },
  data () {
    return {
      
    }
  },
  methods:{
    
   
  },
  created(){
    
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 0;
}
a {
  color: #42b983;
}

</style>
