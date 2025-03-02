<script setup lang="ts">
import { nanoid } from "nanoid";
import type { Column, Task } from "~/types";

const taskList = <Task[]>[
	{
		id: nanoid(),
		title: "Create marketing landing page",
		createdAt: new Date(),
	},
	{
		id: nanoid(),
		title: "Develop Cool new Feature",
		createdAt: new Date(),
	},
	{
		id: nanoid(),
		title: "Fix Page Nav Bug",
		createdAt: new Date(),
	},
];

const columns = ref<Column[]>([
	{
		id: nanoid(),
		title: "Backlog",
		tasks: taskList,
	},
	{
		id: nanoid(),
		title: "In Progress",
		tasks: [],
	},
	{
		id: nanoid(),
		title: "QA",
		tasks: [],
	},
	{
		id: nanoid(),
		title: "Complete",
		tasks: [],
	},
]);
</script>

<template>
	<h1>Hello World</h1>
</template>
