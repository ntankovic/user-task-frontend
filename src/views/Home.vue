<template>
    <div class="home">
        <div class="position-absolute top-50 start-50 translate-middle">
            <select class="form-select" aria-label="Process" v-model="selected_model">
                <option value="">Odaberi proces...</option>
                <option v-for="m in models" v-bind:key="m.model_path" :value="m.model_path">
                    {{ m.main_process.name }}
                </option>
            </select>
            <br />
            <br />
            <button class="btn btn-primary" v-on:click="start_bpmn_proces">Start</button>
        </div>
    </div>
</template>

<script>
// @ is an alias to /src

export default {
    name: 'Home',
    components: {},
    data() {
        return {
            selected_model: '',
            instance: '',
            task: '',
            models: [],
        };
    },
    async mounted() {
        let r = await fetch(`${process.env.VUE_APP_BPMN_SERVER}/model`);
        this.models = (await r.json()).results;
    },
    methods: {
        start_bpmn_proces() {
            if (!this.selected_model) {
                return;
            }
            fetch(`${process.env.VUE_APP_BPMN_SERVER}/model/${this.selected_model}/instance`, {
                method: 'POST',
            })
                .then((response) => {
                    return response.json();
                })
                .then((data) => {
                    this.instance = data.id;
                    fetch(`${process.env.VUE_APP_BPMN_SERVER}/instance/${this.instance}`, {
                        method: 'GET',
                    })
                        .then((response) => {
                            return response.json();
                        })
                        .then((data) => {
                            this.task = data.pending[0];
                            this.$router.push(`/form/${this.instance}/${this.task}`);
                        });
                });
        },
    },
};
</script>
