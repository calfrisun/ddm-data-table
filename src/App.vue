<script lang="ts">
import {defineComponent, onMounted, PropType, ref, useSlots, watch} from 'vue';

export interface headerInterface {
    key: string;
    width: number | undefined;
}

export default defineComponent({
    name: 'ddm-data-table',
    props: {
        header: {
            type: Object as PropType<Array<headerInterface>>,
            require: true,
        },
        dataList: {
            type: Object as PropType<Array<any>>,
            require: true,
        },
        clickItem: {
            type: Function as PropType<(item: any) => void>,
            required: false,
            default: () => ({}),
        },
    },
    setup(props) {
        const innerHeader = ref<headerInterface[] | undefined>();
        const innerDataList = ref<any>();
        const slots = useSlots();

        const onClickItem = (item: any, colums: headerInterface) => {
            if (colums.key.toLowerCase().indexOf('btn') > -1) return;
            props.clickItem(item);
        };

        watch(
            () => props.header,
            () => {
                innerHeader.value = props.header as Array<headerInterface>;
            },
        );
        watch(
            () => props.dataList,
            () => {
                innerDataList.value = props.dataList as Array<any>;
            },
        );

        onMounted(() => {
            if (props.header) {
                innerHeader.value = props.header as Array<headerInterface>;
            }
            if (props.dataList) {
                innerDataList.value = props.dataList as Array<any>;
            }
        });

        return {
            slots,
            innerHeader,
            innerDataList,
            onClickItem,
        };
    },
});
</script>
<template>
    <section>
        <table>
            <thead>
            <tr>
                <th class="text-center" v-for="(item, index) in innerHeader" :key="index">{{ item.key }}</th>
            </tr>
            </thead>
            <tbody v-if="innerDataList && innerDataList.length > 0">
            <tr v-for="(data, index) in innerDataList" class="layout-table-tr" :key="data">
                <td v-for="item in innerHeader" class="text-center" @click="onClickItem(data, item)">
                    <slot v-if="slots[item.key]" :name="item.key" :index="index" :value="data[item.key]"
                          :item="data"></slot>
                    <span v-else>
                    {{ data[item.key] }}
                    </span>
                </td>
            </tr>
            </tbody>
        </table>
    </section>
    <div v-if="!innerDataList" style="text-align: center; color: #999999">No data available</div>
</template>
