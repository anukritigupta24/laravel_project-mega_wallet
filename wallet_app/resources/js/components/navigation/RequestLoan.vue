<template>

    <div class="row justify-content-center">
        <div class="col-md-10 mt-5">

            <div class="card card-primary">
                <div class="card-header bg-gradient-yellow">
                    <h3 class="card-title">Request Loan</h3>
                </div>
                <!-- /.card-header -->
                <!-- form start -->
                <form @submit.prevent="save" class="p-2">

                    <div class="card-body">
                        <div class="form-group">
                            <label>Enter Amount</label>
                            <input type="text" class="form-control" v-model="amount" placeholder="Enter amount" name="amount">

                            <!--                            <p class="text-danger" v-text="errors.get('amount')"></p>-->
                        </div>

                        <!-- /.card-body -->
                        <div class="card-footer">
                            <button type="submit" class="btn bg-gradient-yellow">Submit</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
        <loan-requests :loan-requests="loanRequests"></loan-requests>
    </div>

</template>

<script>

    class Errors{
        constructor(){
            this.errors = {};
        }

        get(field){
            if(this.errors[field]){
                return this.errors[field][0];
            }
        }

        record(errors){
            this.errors = errors;
        }
    }



    export default {

        data(){

            return{
                amount : '',
                //errors : new Errors(),
                errors : [],
                loanRequests: [],


            }
        },


        created(){
            axios.get('/api/get/loan/requests')
                .then(response => {
                    this.loanRequests = response.data;
                    setTimeout(()=>{
                        $('#loan-tbl').footable();
                    },1000);
                });
        },


        methods:{
            save(){
                axios.post('/api/wallet/request/loan', {'amount' : this.amount})
                    .then(response => {
                        swal.fire({
                            icon: 'success',
                            title: 'Bravo!',
                            text: 'You have successfully created loan request',
                            type: 'success',
                            backdrop: 'rgba(0, 0, 123, 0.4)',
                        });

                        axios.get('/api/get/loan/requests')
                            .then(response => {
                                this.loanRequests = response.data;
                                setTimeout(()=>{
                                    $('#loan-tbl').footable();
                                },1000);
                            });
                    })
                    .catch(error => {
                        // this.errors.record(error.response.data)
                        let msgs = Object.values(error.response.data.errors);
                        this.errors = [].concat.apply([], msgs);
                        swal.fire({
                            icon:'error',
                            title: 'Error occurred',
                            text: this.errors,
                            type: "error",
                            backdrop: `rgba(0, 0, 123, 0.4)`
                        })
                    });
            }
        },
    }
</script>
