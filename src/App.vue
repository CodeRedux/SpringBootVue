<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import InvoiceForm from './components/InvoiceForm.vue'
import InvoiceList from './components/InvoiceList.vue'
import { API_CONFIG } from '@/config'; 

//console.log(API_CONFIG.baseURL);

const invoices = ref([])


async function fetchInvoices() {
try {
const res = await axios.get(API_CONFIG.baseURL+'api/invoices')
invoices.value = res.data
} catch (e) {
console.error(e)
}
}


onMounted(fetchInvoices)
</script>
<template style="margin-top:350px;">
<main style="font-family: Arial, Helvetica, sans-serif; margin:24px; margin-top:20%;">
<h1>Invoice Generator</h1>
<div  style="display:flex;gap:24px;">
<div style="flex:1;">
<InvoiceForm @saved="fetchInvoices" />
</div>
<div style="width:900px;">
<InvoiceList :invoices="invoices" style="overflow: scroll; max-height: 750px; background-color:white; "/>
</div>
</div>
</main>
</template>


