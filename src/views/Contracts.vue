<template>
    <div class="">
        <div class="narrow-container">
            <f-view-heading>
                <h1>
                    {{ $t("view_contract_list.title") }}
                    <span v-if="dRecordsCount" class="f-records-count">
                        ({{ dRecordsCount }})
                    </span>
                </h1>
            </f-view-heading>
            </div>
        
            <div class="div-container" style="padding-top:20px">
                <f-contract-list @records-count="onRecordsCount" />
            </div>
    </div>
</template>

<script>
import FViewHeading from "../components/FViewHeading.vue";
import FContractList from "../data-tables/FContractList.vue";

export default {
    name: "Contracts",

    components: { FContractList, FViewHeading },

    data() {
        return {
            dRecordsCount: 0
        };
    },

    methods: {
        onRecordsCount(_num) {
            this.dRecordsCount = _num;
        }
    }
};
</script>
