<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import { API_CONFIG } from '@/config'; 

//console.log(API_CONFIG.baseURL);
const invoices = ref([]);
const loading = ref(true);
//const baseURL = import.meta.env.production.VITE_API_BASE_URL;
/**
 * Fetch all invoices from Spring Boot backend
 */
const fetchInvoices = async () => {
  try {
   // alert(API_CONFIG.baseURL);
    const response = await axios.get(API_CONFIG.baseURL+"api/invoices");
    invoices.value = response.data || [];
  } catch (error) {
    console.error("Error fetching invoices:", error);
    alert("Failed to fetch invoices from backend.");
  } finally {
    loading.value = false;
  }
};

/**
 * Downloads the PDF of an invoice by calling Spring Boot backend.
 */
const downloadInvoicePdf = async (id) => {
  try {
    const response = await axios.get(API_CONFIG.baseURL+"api/invoices/${id}/pdf", {
      responseType: "blob", // Important for binary data
    });

    const blob = new Blob([response.data], { type: "application/pdf" });
    const url = window.URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = url;
    link.download = `invoice-${id}.pdf`;
    document.body.appendChild(link);
    link.click();
    link.remove();
    window.URL.revokeObjectURL(url);
  } catch (error) {
    console.error("Error downloading invoice PDF:", error);
    alert("Failed to download invoice PDF");
  }
};

const deleteInvoicePdf = async (id) => {
  confirm("Delete this invoice?");
    try {
   // alert(API_CONFIG.baseURL);
    const response = await axios.get(API_CONFIG.baseURL+"api/invoices/delete/"+id);
    invoices.value = response.data || [];
  } catch (error) {
    console.error("Error fetching invoices:", error);
    alert("Failed to fetch invoices from backend.");
  } finally {
    loading.value = false;
    fetchInvoices()
  }
}

// Load invoices automatically on component mount
onMounted(() => {
  fetchInvoices();
});
</script>


<template>
  <div class="p-4">
    <h2 class="text-xl font-bold mb-4" style="text-align: center;">Invoice List</h2>

    <!-- Loader -->
    <p v-if="loading" class="text-gray-500 italic">Loading invoices...</p>

    <!-- Show invoices if available -->
    <div v-else-if="invoices && invoices.length" style="text-align: center;">
      <table class="table" style="width:100%;">
    <thead>
      <tr>       <th>Customer Name <!--<h3></h3>--></th> 
        <th>Subtotal<!--<h3></h3>--></th>
        <th>Items<!--<h3></h3>--></th>
         <th>Download<!--<h3></h3>--></th> 
         <th>Delete<!--<h3></h3>--></th>
      </tr>
    </thead>
    <tbody>
      
      
      <tr  v-for="(inv, index) in invoices"
        :key="inv.id"><td>
        <p class="mb-2">
          {{ inv.customer }}
        </p></td>

       <td>
        <p class="mb-2"><strong></strong> ₹{{ inv.subtotal.toFixed(2) }}</p></td>
        <!-- Items List -->
        <td>
        <details class="mb-4">
          <summary>Items ({{ inv.items?.length || 0 }})</summary>
          <ul v-if="inv.items && inv.items.length" class="ml-5 mt-2 list-disc">
            <li v-for="it in inv.items" :key="it.id">
              {{ it.description }} — {{ it.qty }} × ₹{{ it.unitPrice.toFixed(2) }}
            </li>
          </ul>
          <p v-else class="text-gray-500 italic mt-2">No items found</p>
        </details>
</td>
        <!-- Download PDF Button -->
         <td>
        <button
          @click="downloadInvoicePdf(inv.id)"
          class="px-3 py-1 bg-blue-600 text-white rounded hover:bg-blue-700 transition btn btn-success">          📄 Download PDF</button>
       </td>
             <td>
       
        <button
          @click="deleteInvoicePdf(inv.id)"
          class="glyphicon glyphicon-trash btn btn-danger">          📄 Delete</button>
             </td>
      </tr>  </tbody>
         </table> 
      </div>  <p v-else class="text-gray-500 italic">No invoices available</p>
   

     
     </div>
 
    <!-- If no invoices -->
    
  <!-- </div> -->
</template>



<style scoped>
summary {
  cursor: pointer;
  font-weight: 500;
}
button {
  font-weight: 500;
}
</style>