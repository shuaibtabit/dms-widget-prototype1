<template>
    <div>
    <table>
        <thead>
            <tr>
                <th v-for="header in headers" :key="header">{{ header }}</th>
            </tr>
        </thead>
        <tbody>
        <!-- Render rows for each item in dataset -->
        <tr v-for="(row, index) in dataset" :key="index">
          <!-- Render cells for each header (column) in the row -->
          <td v-for="header in headers" :key="header">{{ row[header] }}</td>
        </tr>
      </tbody>
    </table>
</div>
    
</template>
<script>

export default{
    props: {
        csvLink:String
    },
    data(){
        return {
            dataset : [],
            headers : [],
            dummyheader : [1,2,3,4],
            dummydata : [2,3,4,4],
            name : "Shuai"
        }
    },
    mounted(){
        this.fetchCSV();
    },
    methods:{
        fetchCSV(){
            fetch(this.csvLink)
                .then(response => response.text())
                .then(csvData =>   
                    this.parseCSV(csvData)
                )
                .catch(error => console.log(error))
    
        },
    
        parseCSV(csvData){
            const rows = csvData.split("\n")
            this.headers = rows[0].split(",");
            const data = rows.slice(1).map(row=>{
                const values = row.split(",");
                const obj = {}
                this.headers.forEach((header, index)=>{
                    obj[header] = values[index];
                })
                return obj;
            })
            this.dataset = data;
        }

    }
}
{/* <tbody>
            <tr v-for="(row, index) in dataset" :key="index"></tr>
            <td v-for="header in headers" :key="header">{{ row[header] }}</td>
        </tbody> */}

    //     <div v-if="loading">Loading...</div>
    //   <div v-if="error">{{ error }}</div>
  
    //   <div v-if="results && results.length">
    //     <h3>Results for "{{ query }}" ({{ selectedMutationType }})</h3>
    //     <div v-for="(result, index) in results" :key="index">
    //       <pre>{{ result }}</pre>
    //     </div>
    //   </div>

</script>