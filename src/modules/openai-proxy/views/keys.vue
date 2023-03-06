<template>
	<cl-crud ref="Crud">
		<cl-row>
			<!-- 刷新按钮 -->
			<cl-refresh-btn />
			<!-- 新增按钮 -->
			<cl-add-btn />
			<!-- 删除按钮 -->
			<cl-multi-delete-btn />
			<cl-flex1 />
			<!-- 关键字搜索 -->
			<cl-search-key />
		</cl-row>

		<cl-row>
			<!-- 数据表格 -->
			<cl-table ref="Table" />
		</cl-row>

		<cl-row>
			<cl-flex1 />
			<!-- 分页控件 -->
			<cl-pagination />
		</cl-row>

		<!-- 新增、编辑 -->
		<cl-upsert ref="Upsert" />
	</cl-crud>
</template>

<script lang="ts" name="openai_proxy-keys" setup>
import { useCrud, useTable, useUpsert } from "@cool-vue/crud";
import { ElMessage } from "element-plus";
import { useCool } from "/@/cool";

const { service } = useCool();

// cl-upsert 配置
const Upsert = useUpsert({
	items: [
		{ label: "Key", prop: "key", required: true, component: { name: "el-input" } },
		{
			label: "状态",
			prop: "status",
			component: {
				name: "el-switch",
				props: {
					activeValue: 1,
					inactiveValue: 0
				}
			},
			required: true
		},
		{
			label: "过期时间",
			prop: "expire_time",
			component: {
				name: "el-date-picker",
				props: { type: "datetime", valueFormat: "YYYY-MM-DD HH:mm:ss" }
			}
		},
		{ label: "总授权次数", prop: "total_granted", component: { name: "el-input" } },
		{ label: "总使用次数", prop: "total_used", component: { name: "el-input" } },
		{ label: "总可用次数", prop: "total_available", component: { name: "el-input" } },
		{
			label: "备注",
			prop: "remark",
			component: { name: "el-input", props: { type: "textarea", rows: 4 } }
		}
	]
});

// cl-table 配置
const Table = useTable({
	columns: [
		{ type: "selection" },
		{ label: "id", prop: "id" },
		{ label: "创建时间", prop: "createTime" },
		{ label: "更新时间", prop: "updateTime" },
		{ label: "Key", prop: "key", showOverflowTooltip: true },
		{ label: "状态", prop: "status", component: { name: "cl-switch" } },
		{ label: "过期时间", prop: "expire_time" },
		{ label: "总授权", prop: "total_granted" },
		{ label: "总使用", prop: "total_used" },
		{ label: "总可用", prop: "total_available" },
		{ label: "备注", prop: "remark", showOverflowTooltip: true },
		{
			type: "op",
			buttons: [
				"edit",
				"delete",
				{
					label: "更新",
					type: "success",
					onClick({ scope }) {
						service.openai_proxy.keys
							.check_key({ id: scope.row.id })
							.then((res) => {
								console.log(res);
								ElMessage.success(res);
								Crud.value?.refresh();

							})
							.catch((err) => {
								console.log(err);
								ElMessage.error(err);
							});
					}
				}
			]
		}
	]
});

// cl-crud 配置
const Crud = useCrud(
	{
		service: service.openai_proxy.keys
	},
	(app) => {
		app.refresh();
	}
);
</script>
