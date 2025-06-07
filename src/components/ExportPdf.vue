<template>
    <CustomButton btn-type="primary" @click="exportPdf">
        Export resume as pdf
    </CustomButton>
</template>

<script>
import CustomButton from './CustomButton.vue';

export default {
    components: {
        CustomButton
    },
    methods: {
        exportPdf() {
            const pdfConfig = {
                margin: 0,
                filename: 'resume',
                jsPDF: {
                    unit: 'mm',
                    format: 'a4'
                }
            }
            const resume = document.getElementById('resume');
            html2pdf().set(pdfConfig).from(resume).save();
        }
    }
}
</script>