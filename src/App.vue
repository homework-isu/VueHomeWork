<template>
    <div id="app" class="container mt-5">
        <div class="card p-4" style="background-color: #fafffb;">
            <h1 class="mb-4" style="color: #011f08;">Управление записями</h1>

            <form @submit.prevent="addRecord">

                <div class="mb-3 form-group row">
                    <label for="recordText" class="col-sm-2 col-form-label">Текст записи:</label>
                    <div class="col-sm-10">
                        <input type="text" id="recordText" v-model="newRecordText" class="form-control"
                            placeholder="Введите текст для записи" required>
                    </div>
                </div>

                <div class="mb-3 d-flex ">
                    <label class="col-sm-2 col-form-label">Тип записи:</label>
                    <div class="col-sm-10 d-flex justify-content-start">
                        <div class="form-check form-check-inline">
                            <input type="radio" id="work" value="рабочая" v-model="newRecordType"
                                class="form-check-input">
                            <label for="work" class="form-check-label">Рабочая</label>
                        </div>

                        <div class="form-check form-check-inline">
                            <input type="radio" id="personal" value="линая" v-model="newRecordType"
                                class="form-check-input">
                            <label for="personal" class="form-check-label">Личная</label>
                        </div>
                    </div>
                </div>

                <div class="mb-3 form-group row">
                    <label class="col-sm-2 col-form-label">Приоритет:</label>
                    <div class="col-sm-10">
                        <div class="input-group">
                            <select v-model="newRecordPriority" class="form-select">
                                <option value="low">низкий</option>
                                <option value="hight">высокий</option>
                            </select>
                        </div>
                    </div>
                </div>

                <button type="submit" class="btn btn-success">Добавить запись</button>
                <button type="button" @click="deleteAllRecords" class="btn btn-danger ms-2">Удалить все записи</button>

            </form>

            <ul class="list-group mt-3  no-border">
                <li v-for="(record, index) in sortedRecords" :key="index" class="list-group-item record">
                    <div class="d-flex justify-content-between align-items-center">
                        <div :class="{ 'text-decoration-line-through': record.done, 'hight': record.priority ==
                            'hight', 'low': record.priority != 'hight' }"
                            style="color: #155724;">
                            <strong>{{ record . type + " запись :" }}</strong> {{ record . text }}
                        </div>
                        <div>
                            <div class="form-check d-flex align-items-center">
                                <input type="checkbox" v-model="record.done" class="form-check-input"
                                    :id="'doneItem' + index">
                                <label :for="'doneItem' + index" class="form-check-label ms-2"
                                    style="color: #155724;">Сделано</label>
                                <button @click="deleteRecordById(index)" class="btn btn-danger ms-2">Удалить</button>
                            </div>
                        </div>
                    </div>
                </li>
            </ul>

        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                newRecordText: '',
                newRecordType: 'линая',
                newRecordPriority: 'low',
                records: JSON.parse(localStorage.getItem('records')) || [],
            };
        },
        computed: {
            sortedRecords() {
                return this.records.slice().sort((a, b) => {
                    const priorityOrder = {
                        'low': 1,
                        'hight': 2
                    };
                    const priorityComparison = priorityOrder[b.priority] - priorityOrder[a.priority];
                    return priorityComparison !== 0 ? priorityComparison : (b.done - a.done);
                });
            },
        },
        methods: {
            addRecord() {
                const newRecord = {
                    text: this.newRecordText,
                    type: this.newRecordType,
                    priority: this.newRecordPriority,
                    done: false,
                };
                if (this.newRecordText.length === 0) {
                    return;
                }

                this.records.push(newRecord);
                this.storeData();
                this.clearFormForRecord();
            },
            storeData() {
                localStorage.setItem('records', JSON.stringify(this.records));
            },
            clearFormForRecord() {
                this.newRecordText = '';
                this.newRecordType = 'personal';
                this.newRecordPriority = 'low';
            },
            deleteAllRecords() {
                localStorage.removeItem('records');
                this.records = [];
            },
            deleteRecordById(index) {
                this.records.splice(index, 1);
                this.storeData();
            },
        },
    };
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        text-align: center;
        color: #219b3d;
        margin-top: 60px;
    }

    .hight {
        font-weight: 900;
    }

    .low {
        font-weight: 300;
    }

    .record {
        margin: 10px 0;
        border-radius: 8px !important;
    }

    .no-border {
        border: none !important;
    }
</style>
