<template>
    <Dialog v-model:visible="isSendFile" modal class="send-message-dialog" style="width: 30rem;">
        <template #header>
            <div class="flex gap-5 align-items-center py-3 px-5">
                <Button @click="handleClostDialog" icon="pi pi-times" severity="secondary" rounded class="btn btn-close" />
                <span class="font-semibold text-xl dark-gray">
                    Send Photo
                </span>
            </div>
        </template>
        <template #default>
            <div class="img-preview my-2">
                <img :src="file" alt="" class="w-full h-full">
            </div>
        </template>
        <template #footer>
            <div class="flex gap-2 align-items-center justify-content-between shadow-2 py-1 px-1">
                <TextArea v-model="textFile" class="font-hanuman ms-image-send" :rows="1" auto-resize placeholder="Say someting ..."
                    :spellcheck="false" />
                <span class="align-self-end mb-1">
                    <Button size="small" icon="pi pi-send" label="Send" class="btn bg-base" @click="sendMessage"
                        :loading="loading" />
                </span>
            </div>
        </template>
    </Dialog>
    <div class="w-full">
        <div class="flex gap-2">
            <span class="relative w-full">
                <TextArea v-model="messageText" placeholder="Say something..." class="ms-input-box"
                    :class="isEnglish ? 'font-roboto' : 'font-hanuman'" :rows="1" :spellcheck="false"
                    auto-resize></TextArea>
                <span class="file-input">
                    <label for="file" class="cursor-pointer">
                        <input type="file" id="file" ref="fileInput" hidden accept="image/png, image/gif, image/jpeg"
                            @change="fileToBase64">
                        <i class="pi pi-file" style="font-size: 20px;color: gray;"></i>
                    </label>
                </span>
                <span class="send-message-button">
                    <Button id="btn-sent" @click="sendMessage" :disabled="!messageText" rounded :loading="loading"
                        icon="pi pi-send" class="button-no-shadow"
                        :class="messageText ? 'button-icon-base' : 'bg-gray-500'">
                    </Button>
                </span>
            </span>
        </div>
    </div>
</template>
    
<script setup>
import Dialog from "primevue/dialog"
import Button from "primevue/button"
import { computed, ref } from "vue"
import API from "../service"
import { useStore } from "../store"
import { useRoute } from "vue-router"
import TextArea from "primevue/textarea"
import { useToast } from "primevue/usetoast"


const toast = useToast()
const api = new API()
const store = useStore()
const messageText = ref("")
const textFile = ref("")
const isSendFile = ref(false)
const file = ref("")
const route = useRoute()
const loading = ref(false)
const fileInput = ref(null)

const props = defineProps({
    addMessage: {
        type: Function,
        required: true,
    },
    scrollToLatesMessage: {
        type: Function,
        required: true,
    },
})

function fileToBase64(e) {
    if (!e?.target?.files[0]) return
    if (!isImage(e.target.files[0].type)) {
        addToast('only image allow share', 'warn')
        return
    }
    const reader = new FileReader()
    reader.readAsDataURL(e.target.files[0])
    reader.onload = () => {
        file.value = reader.result;
        isSendFile.value = true
    }
}
const sendMessage = async () => {
    let { id } = route.params
    if (!id || (!messageText.value && !file.value && !textFile.value)) return
    try {
        loading.value = true
        const { data, response } = await api.SEND_MESSAGE({
            room: id,
            text: isSendFile.value ? textFile.value : messageText.value,
            file: file.value,
            sender: store.user.information._id
        })
        if (response) return console.log({ response });
        if (data.data.statusCode == 201) {
            props.addMessage(data.data.sent)
            store.socketSendMesage({ message: data.data.sent, sendTo: getFriendIdFromRoom(id) })
            messageText.value = ""
            setTimeout(() => {
                props.scrollToLatesMessage()
            }, 200)
        }
        if (file.value) {
            handleClostDialog()
        }
    } catch (error) {
        console.log({ error });
    }
    finally {
        loading.value = false
    }


}

const handleClostDialog = () => {
    isSendFile.value = false
    file.value = ""
    textFile.value = ""
    fileInput.value.value = ""
}

const isImage = (type) => (type == "image/png" || type == "image/jpeg" || type == "image/jpg")

const addToast = (ms, severity) => {
    toast.add({
        summary: "KHMER CHAT",
        detail: ms,
        life: 5000,
        severity
    })
}

const getFriendIdFromRoom = id => {
    let room = store.rooms?.find(ro => ro._id == id)
    if (!room) return null
    let { members } = room
    if (!members?.length) return null
    return members?.find(mem => mem._id != store.user?.information?._id)?._id
}
const isEnglish = computed(() => {
    let regExp = /[a-zA-Z]/
    return regExp.test(messageText.value)
})

</script>





<style scoped>

#btn-sent:hover>span #voice-icon,
#btn-sent:hover>span #sent-icon {
    fill: white !important;
    color: white !important;
}

</style>    