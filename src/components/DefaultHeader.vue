<template>
    <div class="default-header" style="height: 8vh;">
        <div class="relative col-2">
            <span v-if="!store.isSearch">
                <Button icon="pi pi-bars" rounded class="button-no-shadow nav-button" @click="toggleNavbar"/>
            </span>
            <span v-else>
                <Button class="animation-arrow arrow-button button-no-shadow" @click="store.unSetSearch" icon="pi pi-arrow-left" rounded size="small" />
            </span>
        </div>
        <div class="col-10">
            <span class="relative w-full">
                <input type="search" placeholder="Search..." @focus="store.setSearch" v-model="store.searchText"
                class="search-input w-full"
                :spellcheck="false" style="padding: .7rem;">
                <span class="icons-search">
                    <i class="pi pi-search"></i>
                </span>
            </span>
        </div>
    </div>
</template>

<script setup>
import Button from "primevue/button";
import { useStore } from "../store";


const store = useStore()

const props = defineProps({
    toggleNavbar:{
        type : Function,
        required : true
    }
})

</script>



<style >

.dropdown-menu button {
    width: 2.5rem !important;
    height: 2.5rem !important;
    border: 1px solid #6A83A6 !important;
    border: none !important;
}
.dropdown-menu button .pi {
    font-size: 1.3rem !important;
}
.dropdown-menu button:focus {
    box-shadow: none !important;
}
.dropdown-menu button span:nth-child(1) {
    color: #6A83A6;
} 
.dropdown-menu {
    top: -10px !important;
}
.dropdown-menu .p-speeddial-action {
    width: 2.8rem !important;
    height: 2.8rem !important;
}
.profile-image {
    display: inline-block;
    width: 2.8rem;
    height: 2.8rem;
    border-radius: 50%;
    overflow: hidden;
    /* border: 1px solid; */
    cursor: pointer;
    box-shadow: 0 0 1px 1px gray;
}
.icons-search {
    position: absolute;
    top: 1px;
    left: 15px;
    color: #9a9a9a;
}
.search-input {
    padding: 10px 0px 10px 40px !important;
    outline: none;
    border: 1px solid #D8D3DD;
    width: 100%;
    border-radius: 20px;
    color: #7B7B7B;
    transition: all .3s linear;
}
.search-input:focus {
    box-shadow: 0 0 0 1px #537BCC;
}
.search-input::placeholder {
    font-size: 14px;
}
.search-input:focus + .icons-search {
    color: #537BCC;
}
.search-input:focus::placeholder {
    color: #537BCC;
}

::-webkit-search-cancel-button {
    display: none;
}
.default-header {
    position: fixed;
    background-color: #fff;
    top: 0;
    width: 390px;
    left: 0;
    z-index: 2;
    display: flex;
    align-items: center;
    padding: auto 10px;
}
@media only screen and (max-width:800px) {
    .default-header {
        width: 100%;
    }
}

</style>