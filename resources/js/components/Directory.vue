<template>
    <div>
        <div class="mb-2">
            <label for="name" class="form-label">Name</label>
            <input
                type="text"
                placeholder="Enter Name"
                class="form-control"
                id="name"
                v-model="item.name"
            >
        </div>
        <div class="mb-2">
            <label for="tel" class="form-label">Telephone Number</label>
            <input
                type="text"
                placeholder="Enter Telephone number"
                class="form-control"
                id="tel"
                v-model="item.tel"
            >
        </div>
        <div class="d-grid">
            <button class="btn btn-success"
                    @click="save"
            >
                {{ isEditing ? "Update" : "Save" }}
            </button>
        </div>

        <div>
            <h3 class="text-center" v-if="lists.length > 0">All Numbers</h3>
            <ul class="list-group">
                <li
                    class="list-group-item"
                    v-for="tel in lists"
                    :key="tel.id"
                >
                    {{ tel.name }} - {{ tel.tel }}
                    <span class="float-right">
                        <button
                            class="btn btn-warning btn-sm mr-2"
                            @click="editTel(tel)"
                        >Edit</button>
                        <button
                            class="btn btn-danger btn-sm mr-2"
                            @click="deleteTel(tel.id)"
                        >Delete</button>
                    </span>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
export default {
    name: "Directory",
    data() {
        return {
            lists: [],
            item: {
                name: "",
                tel: ""
            },
            isEditing: false,
            temp_id: null
        }
    },
    mounted() {
        this.fetchAll()
    },
    methods: {
        fetchAll() {
            try {
                axios.get('/api/tel')
                    .then(res => this.lists = res.data)
            } catch (e) {
                console.log(e)
            }
        },

        save() {
            let method = axios.post
            let url = "/api/tel"
            if (this.temp_id) {
                method = axios.put
                url = `/api/tel/${this.temp_id}`
            }
            try {
                method(url, this.item)
                    .then(res => {
                        this.fetchAll()
                        this.item = {
                            name: "",
                            tel: ""
                        }
                        this.temp_id = null
                        this.isEditing = false
                    })
            } catch (e) {
                console.log(e)
            }
        },

        editTel(tel) {
            this.item = {
                name: tel.name,
                tel: tel.tel,
            }
            this.temp_id = tel.id;
            this.isEditing = true
        },


        deleteTel(id) {
            try {
                axios.delete(`/api/tel/${id}`)
                    .then(res => {
                        this.fetchAll()
                    })
            } catch (e) {
                console.log(e)
            }
        }
    }
}
</script>

<style scoped>

</style>
