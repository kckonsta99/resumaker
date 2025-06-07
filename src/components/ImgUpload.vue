<template>
    <label>
        Upload Image
        <input type="file" accept="image/jpeg image/png image/jpg" @change="changeImage">
    </label>    
</template>

<script>
export default {
    methods: {
        changeImage(event) {
            const selectedFile = event.target.files[0];
            if (selectedFile) {
                const reader = new FileReader();
                reader.addEventListener('load', () => {
                    this.$emit('imageChanged', reader.result)
                });
                reader.readAsDataURL(selectedFile);
            }
        }
    }
}
</script>

<style scoped>

input[type="file"] {
    display: none;
}

label {
    display: block;
    border-radius: 4px;
    padding: 8px 10px;
    text-align: center;
    border: none;
    font-size: 16px;
    font-weight: 600;
    background: #ffb347;
    background: -webkit-linear-gradient(to right, #ffcc33, #ffb347);
    background: linear-gradient(to right, #ffcc33, #ffb347);
    cursor: pointer;
}
</style>