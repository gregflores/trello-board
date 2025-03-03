<script setup lang="ts">
import { nanoid } from "nanoid";
import type { Column, Task } from "~/types";
import draggable from "vuedraggable";

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

const columns = useLocalStorage<Column[]>(
	"trelloBoard",
	[
		{
			id: nanoid(),
			title: "Backlog",
			tasks: taskList,
		},
		{
			id: nanoid(),
			title: "Selected for dev",
			tasks: [],
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
	],
	{
		serializer: {
			read: (value) => {
				return JSON.parse(value).map((column: Column) => {
					column.tasks = column.tasks.map((task: Task) => {
						task.createdAt = new Date(task.createdAt);
						return task;
					});
					return column;
				});
			},
			write: (value) => JSON.stringify(value),
		},
	}
);

const alt = useKeyModifier("Alt");

const createColumn = () => {
	const column: Column = {
		id: nanoid(),
		title: "",
		tasks: [],
	};
	columns.value.push(column);
	nextTick(() => {
		(
			document.querySelector(
				".column:last-of-type .title-input"
			) as HTMLInputElement
		).focus();
	});
};
</script>

<template>
	<div class="flex items-start overflow-x-auto gap-4">
		<draggable
			v-model="columns"
			group="columns"
			item-key="id"
			class="flex gap-4 items-start"
			:animation="150"
			handle=".drag-handle">
			<template #item="{ element: column }: { element: Column }">
				<div class="bg-gray-200 column p-5 rounded min-w-[250px]">
					<header class="font-bold mb-4">
						<Draghandle />
						<input
							type="text"
							class="bg-transparent focus:bg-white rounded px-1 w-4/5 title-input"
							@keyup.enter="
								($event.target as HTMLInputElement).blur()
							"
							@keydown.backspace="
								column.title === ''
									? (columns = columns.filter(
											(c) => c.id !== column.id
									  ))
									: null
							"
							v-model="column.title" />
					</header>
					<draggable
						v-model="column.tasks"
						:group="{ name: 'tasks', pull: alt ? 'clone' : true }"
						item-key="id"
						:animation="150"
						handle=".drag-handle">
						<template #item="{ element: task }: { element: Task }">
							<div>
								<TrelloBoardTask
									:task="task"
									@delete="
										column.tasks = column.tasks.filter(
											(t) => t.id !== $event
										)
									" />
							</div>
						</template>
					</draggable>

					<footer>
						<NewTask @add="column.tasks.push($event)" />
					</footer>
				</div>
			</template>
		</draggable>
		<button
			@click="createColumn"
			class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50">
			+ Add Another Column
		</button>
	</div>
</template>
