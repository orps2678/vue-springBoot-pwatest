<template>
    <div class="container">
        <h1 class="text-center">員工列表</h1>

        <!-- 員工資料查詢功能-->
        <div class="search-form form-group">
            <div class="row">
                <div class="col-md-4"></div>
                <div class="col-md-4">
                    <div class="input-group">
                        <input v-model="searchId" type="text" class="form-control" placeholder="輸入員工編號" />
                        <button @click="getEmployeeById" class="btn btn-primary">查詢</button>
                    </div>
                </div>
                <div class="col-md-4"></div>
            </div>
        </div>

        <!-- 新增員工表單 -->
        <form @submit.prevent="addEmployee">
            <div class="row">
                <div class="col-md-4 form-group">
                    <label for="firstName">員工名：</label>
                    <input v-model="newEmployee.firstName" type="text" class="form-control" required />
                </div>
                <div class="col-md-4 form-group">
                    <label for="lastName">員工姓：</label>
                    <input v-model="newEmployee.lastName" type="text" class="form-control" required />
                </div>
                <div class="col-md-4 form-group">
                    <label for="email">電子郵件：</label>
                    <input v-model="newEmployee.email" type="email" class="form-control" required />
                </div>
            </div>
            <button type="submit" class="btn btn-primary">新增員工資料</button>
        </form>

        <!-- 當前員工列表-->
        <table class="table table-striped">
            <thead>
                <th>員工編號</th>
                <th>員工名</th>
                <th>員工姓</th>
                <th>電子郵件</th>
            </thead>
            <tbody>
                <tr v-for="employee in employees" :key="employee.id">
                <td>{{ employee.id }}</td>
                <td>{{ employee.firstName }}</td>
                <td>{{ employee.lastName }}</td>
                <td>{{ employee.email }}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    // 引入 ref 和 reactive 函數
    import { ref } from 'vue';
    import EmployeeService from '../services/EmployeeService';

    export default {
        name: 'EmployeeList', // 修改組件名稱為 EmployeeList
        setup() {
            // 使用 ref 定義員工數據和搜索的員工編號
            const employees = ref([]);
            const searchId = ref('');

            // 新增員工表單的資料
            const newEmployee = ref({
                firstName: '',
                lastName: '',
                email: '',
            });

            // 定義獲取員工列表的方法
            const getEmployees = () => {
                EmployeeService.getEmployees().then((response) => {
                // 使用 ref 的 .value 屬性來更新員工數據
                employees.value = response.data;
                });
            };

            // 定義根據員工編號查詢員工的方法
            const getEmployeeById = () => {
                EmployeeService.getEmployeeById(searchId.value).then((response) => {
                    // 將查詢到的員工數據存放在一個數組中，並使用 ref 的 .value 屬性來更新員工數據
                    employees.value = [response.data];
                })
                .catch(() => {
                    // 若查詢不到資料，則將員工數據清空
                    employees.value = [];
                });
            };

            const addEmployee = () => {
                EmployeeService.addEmployee(newEmployee.value)
                .then(() => {
                    // 新增成功後重新獲取員工列表，並清空新增員工表單的資料
                    getEmployees();
                    newEmployee.value = {
                        firstName: '',
                        lastName: '',
                        email: '',
                    };
                })
                .catch((error) => {
                    console.error('新增員工失敗:', error);
                });
            };

            // 在組件創建時自動獲取員工列表
            getEmployees();

            // 使用 setup 函數返回員工數據和方法，供模板使用
        return {
            employees,
            searchId,
            newEmployee,
            getEmployeeById,
            addEmployee,
        };
    },
};
</script>

<style>
/* 可以在這裡自訂你想要的樣式 */
</style>
