<template>
    <div class="container">
        <div class="row">
            <div class="col-md-6 mx-auto">
               <form @submit="handleSubmit">
                    <div class="form-group">
                        <input type="text" required v-model="inpValue" class="form-control" placeholder="Enter Item">
                        <button type="submit" class="btn btn-primary">Add</button>
                    </div>
               </form>
            </div>
        </div>
    </div>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">
                <table class="table">
                    <thead>
                        <tr>
                        <th scope="col">#</th>
                        <th scope="col">Todo item</th>
                        <th scope="col">Time</th>
                        <th scope="col">Actions</th>
                        </tr>
                    </thead>
                    <tbody v-if="items.length">
                        <tr v-for="(item,index) in items" :key="index">
                            <th scope="row">{{index+1}}</th>
                            <td>
                                <div v-if="item.isEdit">
                                    <input class="form-control" type="text" v-model="updateValue" >
                                </div>
                                <div v-else>
                                    {{item.name}}
                                </div>
                            </td>
                            <td>{{item.time}}</td>
                            <td>
                                <div v-if="item.isEdit">
                                    <button @click="handleUpdate(index)" class="btn btn-outline-primary">Update</button>
                                    <button @click="handleCancel(index)" class="btn btn-outline-danger">Cancel</button>
                                </div>
                                <div v-else>
                                    <button @click="handleEdit(index)" class="btn btn-outline-primary">Edit</button>
                                    <button @click="handleDelete(index)" class="btn btn-outline-danger">Delete</button>
                                    <button @click="handleShiftUp(index)" class="btn btn-outline-secondary">Shift Up</button>
                                    <button @click="handleShiftDown(index)" class="btn btn-outline-secondary">Shift Down</button>
                                </div>
                            </td>
                        </tr>
                    </tbody>
                    <tbody v-else>
                        <tr>
                            <td colspan="4">
                                No Record Found
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>


<script>
export default{
    name:"Todo",
    data()
    { 
        return{
            items:[],
            inpValue:"",
            updateValue:"hello"
        }
    },
    methods:{
        handleSubmit:function(e){
            e.preventDefault();

            let list = this.items;

            let name = this.inpValue;
            let time = new Date().toLocaleString();
            let isEdit = false;

            let obj = {name,time,isEdit};
            list.push(obj);

            this.items = list;
            this.inpValue = "";

            
            this.UpdateLocalStorage(list);
        },
        UpdateLocalStorage:(list)=>{
            localStorage.setItem('data',JSON.stringify(list))
        },
        handleEdit: function(i){
            let list = this.items;
            list[i].isEdit = true;
            this.updateValue = list[i].name;
            this.items = list

        },
        handleCancel:function(i){
            let list = this.items;
            list[i].isEdit = false;
            this.updateValue = "";
            this.items = list
        },
        handleUpdate:function(i){
            let value = this.updateValue;
            console.log(this.updateValue);
            if(value===''){
                alert("Please enter something in update Field");
            }else{
                let list = this.items;
                list[i].name = value;
                list[i].isEdit = false;
                list[i].time = new Date().toLocaleString();
                this.inpValue="";
                this.UpdateLocalStorage(list);
                this.items = list;
                alert("Todo Updated Sucessfully");
            }
        },
        handleShiftUp:function(i){
            if(i===0){
                alert("You are already on Top");
            }else{
                let list = this.items;
                
                let temp = list[i];
                list[i] = list[i-1];
                list[i-1] = temp;

                this.UpdateLocalStorage(list);
                this.items = list;

            }
        },
        handleShiftDown:function(i){
            if(i===this.items.length-1){
                alert("You are already in most bottom")
            }else{
                let list = this.items;
                
                let temp = list[i];
                list[i] = list[i+1];
                list[i+1] = temp;
                this.UpdateLocalStorage(list);

                this.items = list;
            }
        },
        handleDelete:function(i){
            if(confirm("Are you Sure?")){
                let list = this.items;
                list.splice(i,1);
                this.UpdateLocalStorage(list);
                
                this.items = list;
            }
        }
    },
    mounted:function(){
        let list = JSON.parse(localStorage.getItem("data")) ? JSON.parse(localStorage.getItem("data")) : [];
        this.items = list;
    }
}

</script>


<style scoped>
.form-group{
    display: flex;
}
</style>


