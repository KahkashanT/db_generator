<template>
    <div  >
        <div id="InputFormContainer" class="container"  v-for="(table, tableIndex) in tables">
                    <div >
                        
                        <label  class="col-2">Field Name</label>
                        <label  class="col-2">Type</label>
                        <label class="col-2">Table Name:</label>
                        
                        <input v-model="table.tableName" type="text" col-2>
                        
                        
                        <button class="btn btn-primary col-2 addTable" @click="addTable()" >Add Table</button>
                        <button class="btn btn-primary " v-if="table.foreignTableRltn == true" @click="deleteTable(tableIndex)" ><i class="fa fa-close"></i></button>
                    </div>
                    
                    <div v-if="table.foreignTableRltn == true">
                        
                        <label class="col-1">Foreign tables:</label>
                        <select class="col-2" @change="assignForeignColumns(table.foreignTable,tableIndex)" v-model="table.foreignTable">
                        
                            <option disabled value="">Please select category</option>
                            <option v-for="(eachTable, index2) in tables"  v-bind:value="eachTable.tableName">{{eachTable.tableName}}</option>
                        
                        </select>
                        
                        <label >Column:</label>
                        <label >{{table.foreignTableColumn}}</label>
                       
                        
                        
                    </div>
                    
                    

                    <div v-for="(input, index) in table.inputs" class="form-row">
                        
                        <select class="col-2" @change="assignTypes(tableIndex,index)" v-model="input.category">
                        
                            <option disabled value="">Please select category</option>
                            <option v-for="category in categories"  v-bind:value="category">{{category}}</option>
                        
                        </select>
                        
                        <select class="col-2" v-model="input.type">
                        
                            <option disabled value="">Please select type</option>
                            <option v-for="type in types[index]" v-bind:value="type">{{type}}</option>
                        
                        </select>   
                        
                        <input type="text" v-model="input.fieldName" class="col-2" @change="assignColumn(tableIndex, input.fieldName)" placeholder="Please enter column name">                    
                    
                        <button class="btn btn-primary" @click="deleteField(tableIndex,index)" id="deleteField"><i class="fa fa-close"></i></button>
                    
                    </div>
            
                    <label for="row">Rows : </label>
                    <input v-model.number='table.rows' type="number" id="row"  class="col-1">
                    
                    <label >Primary Key: </label>
                    
                     <select class="col-2" v-model="table.primaryKey">
                       
                         <option disabled value="">Please select type</option>
                        <option v-for="column in table.columns" v-bind:value="column">{{column}}</option>
                    
                     </select> 
                    <button class="btn btn-primary" @click="addField(tableIndex)" id="addField"><i class="fa fa-plus"></i></button>
                    <button  class="btn btn-primary" @click="generateData(tableIndex) " data-toggle="modal" data-target="#myModal" id="generate" >Generate</button> 
                    <button  class="btn btn-primary"  data-toggle="modal" data-target="#myModal"  @click="show(tableIndex)" >Show</button>
                    <Result v-bind:table="resultTable"  v-bind:columns="resultTable.columns" v-bind:colWidth="resultTable.colWidth " ></Result>
        </div>
    </div>
</template>

<script>
    import Result from './Result.vue'
    export default{
            name:'entryHeader',
            data(){
                return{
                    tables:[{
                            tableName:'',
                            foreignTableRltn:false,
                            foreignTable:'',
                            foreignTableColumn:'',
                            foreignTableValues:'',
                            primaryKey:'',
                            inputs:[{
                                category:'',
                                type:'',
                                fieldName:'',
                            }],
                            columns:[],
                            results:[],
                            rows:0,
                            
                    }],
                    
                    categories:[],
                    types:[],
                 
                   resultTable:{},
                    tempResult:'',
                    foreignColumns:[],
                    colWidth:''
                }
            },
            created(){
                
                var result = Object.keys(this.$faker());
                this.categories = result;
                

                
            },
            methods:{
                addField(tableIndex){
                    var self = this;
                    self.tables[tableIndex].inputs.push({
                        category:'',
                        type:'',
                        fieldName:''
                    })
                    document.getElementById('addField').blur();
                },
                
                addTable(){
                    
                    var self = this;                    
                    
                    self.tables.push({
                            tableName:'',
                            foreignTableRltn:true,
                            foreignTable:'',
                            foreignTableColumn:'',
                            foreignTableValues:'',
                            primaryKey:'',
                            inputs:[{
                                category:'',
                                type:'',
                                fieldName:'',
                                
                            }],
                            columns:[],
                            results:[],
                            rows:''
                    })
                      
                },
                deleteTable(index){
                     var self= this;
                     self.tables.splice(index,1)
                },
                
                deleteField(tableIndex,index){
                    var self= this;
                    self.tables[tableIndex].inputs.splice(index,1)
                    document.getElementById('deleteField').blur();
                },
                
                generateData(tableIndex)
                {
                    var self = this;
                    var tempData = new Array();
                    
                     
                    if(self.tables[tableIndex].foreignTableValues.length != 0)
                    {
                        console.log("foreign")
                        for(var j=0; j< self.tables[tableIndex].foreignTableValues.length; j++)
                        {
                            for(var i=0; i < self.tables[tableIndex].rows ; i++)
                            {
                                console.log(self.tables[tableIndex].rows)
                                var tempRow = new Object();
                                self.tables[tableIndex].inputs.forEach(generate);
                                    function generate(value){
                                        var y = new self.$faker()[value.category][value.type]
                                          tempRow[value.fieldName] = y()
                                      }
                                tempRow[self.tables[tableIndex].foreignTableColumn] = self.tables[tableIndex].foreignTableValues[j]
                                 tempData.push(tempRow);                 
                            }
                             
                        }
                        
                    }
                    else{
                        for(var i=0; i < self.tables[tableIndex].rows ;i++)
                        {
                            var tempRow = new Object();
                            self.tables[tableIndex].inputs.forEach(generate);
                                function generate(value){
                                  
                                     var y = new self.$faker()[value.category][value.type]
                                      tempRow[value.fieldName] = y()
                                  }
                            tempData.push(tempRow);                        
                        }
                        
                    }
                    
//                    console.log(tempData)
                    self.tables[tableIndex].results = tempData;
                    self.tempResult = tempData;
                    console.log("table data")
                   // console.log(self.tables[tableIndex])
                   console.log(self.tables[tableIndex].results)                  
                   self.tables[tableIndex].colWidth = (100/self.tables[tableIndex].columns.length)
                    //console.log(self.colWidth)
                    self.show(tableIndex)
                   document.getElementById('generate').blur(); 
                },
                
                assignTypes(tableIndex,index){
                    var self= this;  
                    var faker = self.$faker()
                   
//                    console.log(self.inputs[index])
//                    console.log(faker)
                    for(var value in faker) 
                    {
                        
                        if(self.tables[tableIndex].inputs[index].category == value)
                        {
                            if (faker.hasOwnProperty(value))
                            {
//                                console.log(faker[value])
                                self.types[index] = Object.keys(faker[value]);
                            }
                        }
                    }
                },
                
                assignColumn(tableIndex,fieldName){
//                    console.log(fieldName);
                    this.tables[tableIndex].columns.push(fieldName);
                },
                
                assignForeignColumns(foreignTable,tableIndex){
                    var self = this;
                   var table = self.tables.find(tableName);
                   var column= '';
                   
                   
                   function tableName(value){
                       if(foreignTable == value.tableName)
                       {
                           column = value.primaryKey;                          
                           self.tables[tableIndex].foreignTableColumn = value.primaryKey;
                           self.tables[tableIndex].foreignTableValues = value.results.map(findColumnValue);
                           
                            function findColumnValue(value1){
                                console.log(value1[column])
                               return value1[column]
                           }
                       }
                   }
                   
                   console.log("foreign values")
                   console.log(self.tables[tableIndex].foreignTableValues)
                  
                },
                show(tableIndex){
                    var self = this
                    self.resultTable = self.tables[tableIndex]
                }
                
            },
            components:{
                Result
            }
    }
</script>
<style>
    input,button,select,label{
        margin:5px !important;
        font-size: 20px;
        border-radius: 10px;
        
    }
    div{
        
        margin:auto;
    }
    #InputFormContainer{
        margin-bottom: 10%;
    }
   
    .addTable{
         border-radius: 20px;
    }
    input,select,label{
        padding: 5px;
    }
    .btn{
        border-radius: 10px;
    }
</style>