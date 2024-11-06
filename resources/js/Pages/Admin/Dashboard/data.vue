<script setup>
import { ref, computed, onMounted } from "vue";
import moment from "moment";
import { useForm } from "@inertiajs/inertia-vue3";
import Pagination from "@/Components/Pagination.vue";
import axios from "axios";

const props = defineProps({
    queue: [Object, Array],
    type: [Object, String],
});
const formData = useForm({
    id: null,
    status: null,
    type: null,
    isNew: null,
    clientId: null,
    remarks: null,
    data: null,
});
function getType(type) {
    var text = "";
    switch (type) {
        case "1":
            text = "Business Permit (New)";
            break;
        case "2":
            text = "Building Permit";
            break;
        default:
            text = "Business Permit (Renewal)";
            break;
    }
    return text;
}
function formatDate(date) {
    return moment(date).format("MMMM DD, YYYY h:mm a");
}
onMounted(() => {
    if (performance.navigation.type === performance.navigation.TYPE_RELOAD) {
        if (
            localStorage.getItem("localID") ||
            localStorage.getItem("localtype") ||
            localStorage.getItem("clientID")
        ) {
            formData.id = localStorage.getItem("localID");
            formData.type = localStorage.getItem("localtype");
            formData.clientId = localStorage.getItem("clientID");
            formData.post("/admin/approval/getRecords");
        }
    }
  
});
</script>

<template>
    <div class="flex items-center justify-between">
        <h1 class="text-2xl font-bold text-center">{{ type }}</h1>
        
    </div>

    <div class="card !p-2 mt-2">
        <div>
            <div>
                <table class="w-full text-sm text-left">
                    <thead class="text-md text-gray-700 uppercase">
                        <tr>
                            <th class="cursor-pointer">
                                 Type of Permit
                            </th>
                            <th class="cursor-pointer">
                                 Name of Owner
                            </th>
                            <th class="cursor-pointer">
                                 Project Title
                            </th>
                            <th class="cursor-pointer">
                                 Application Date
                            </th>
                            <th>Remarks</th>
                            <th class="cursor-pointer">
                                  Status
                            </th>
                        </tr>
                    </thead>
                    <tbody>
                        <template v-for="(item, index) in queue" :key="index">
                            <tr>
                                <td>{{ getType(item.type) }}</td>
                                <td>
                                    {{ item.client.lname }},
                                    {{ item.client.fname }}
                                    {{ item.client.mname }}
                                </td>
                                <td>{{ item.project_title }}</td>
                                <td>{{ formatDate(item.application_date) }}</td>
                                <td>{{ item.remarks }}</td>
                                <td class="!py-2 whitespace-nowrap">
								<span class="p-2 rounded" :class="{
									'bg-green-100': item.status == 'Approved',
									'bg-red-100': item.status == 'Rejected',
									'bg-yellow-100': item.status == 'Pending',
								}">
									<i class="mr-2" :class="{
										'fas fa-check-circle text-green-500': item.status == 'Approved',
										'fas fa-times-circle text-red-500': item.status == 'Rejected',
										'fas fa-hourglass-half text-yellow-500': item.status == 'Pending',
									}"></i>
									{{ item.status }}
								</span>
							</td>
                            </tr>
                            <tr class="border-y text-sm text-gray-900"></tr>
                        </template>
                    </tbody>
                </table>

                <!-- No data available message -->

                <!-- Pagination -->
            </div>
        </div>
    </div>
</template>
