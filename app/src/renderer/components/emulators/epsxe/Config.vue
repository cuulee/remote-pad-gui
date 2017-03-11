<template lang="html">
    <emulator-config-modal
        name="ePSXe"
        :isActive="isActive"
        @close="$emit('close')"
        @onSave="save">
        <div slot="form">
            <label class="label">Path to ePSXe binary</label>
            <p class="control has-addons">
                <input type="text" class="input is-expanded"
                    v-model="form.binary" />
                <a class="button is-info" @click="chooseBinary">
                    Browse...
                </a>
            </p>

            <label class="label">Path to PlayStation BIOS</label>
            <p class="control has-addons">
                <input type="text" class="input is-expanded"
                    v-model="form.bios" />
                <a class="button is-info" @click="chooseBios">
                    Browse...
                </a>
            </p>
        </div>
    </emulator-config-modal>
</template>

<script>
import EmulatorConfigModal from 'components/emulators/EmulatorConfigModal'
import settings from 'electron-settings'
import { remote } from 'electron'
const dialog = remote.dialog

export default {
    name: 'epsxe-config',
    components: {
        EmulatorConfigModal
    },
    props: {
        isActive: {
            type: Boolean,
            required: true,
            default: false
        }
    },
    data () {
        return {
            form: {
                binary: '',
                bios: ''
            }
        }
    },
    mounted () {
        settings.get('emulators.epsxe')
            .then(values => {
                this.form = values
            })
    },
    methods: {
        save () {
            settings.set('emulators.epsxe', this.form)
                .then(() => {
                    this.$emit('close')
                })
        },
        chooseBinary () {
            dialog.showOpenDialog({
                title: 'Choose ePSXe binary...',
                properties: ['openFile']
            }, (filenames) => {
                if (filenames) {
                    this.form.binary = filenames[0]
                }
            })
        },
        chooseBios () {
            dialog.showOpenDialog({
                title: 'Choose PlayStation BIOS...',
                properties: ['openFile'],
                filters: [
                    {
                        name: 'PlayStation BIOS',
                        extensions: [
                            'bin',
                            'BIN'
                        ]
                    }
                ]
            }, (filenames) => {
                if (filenames) {
                    this.form.bios = filenames[0]
                }
            })
        }
    }
}
</script>

<style lang="css">
</style>
