<script setup lang="ts">
import Button from '@/components/ui/button/Button.vue';
import { Table, TableBody, TableCaption, TableCell, TableHead, TableHeader, TableRow } from '@/components/ui/table';
import AppLayout from '@/layouts/AppLayout.vue';
import { Employee, SharedData } from '@/types';
import { Head, Link, router, usePage } from '@inertiajs/vue3';
import { CirclePlus, PencilIcon, Trash } from 'lucide-vue-next';
import { computed } from 'vue';

interface EmployeePageProps extends SharedData {
    employees: Employee[];
}

const { props } = usePage<EmployeePageProps>();
const employees = computed(() => props.employees);

const deleteEmploye = async (id: number) => {
    if (!window.confirm('Are you sure want to delete this employee?')) return;

    router.delete(`/employees/${id}`, {
        preserveScroll: true,
        onSuccess: () => {
            router.visit('/employees', { replace: true });
        },
        onError: (errors) => {
            console.log('Error deleting employee:', errors);
        },
    });
};
</script>

<template>
    <Head title="Employees" />

    <AppLayout :breadcrumbs="breadcrumbs">
        <div class="flex h-full flex-1 flex-col gap-4 rounded-xl p-4">
            <div class="flex">
                <Button as-child size="sm" class="bg-indigo-500 text-white hover:bg-indigo-700">
                    <Link href="/employees/create"> <CirclePlus /> Create </Link>
                </Button>
            </div>
        </div>

        <div class="relative min-h-[100vh] flex-1 rounded-xl border border-sidebar-border/70 dark:border-sidebar-border md:min-h-min">
            <Table>
                <TableCaption>Employee List.</TableCaption>
                <TableHeader>
                    <TableRow>
                        <TableHead>Name</TableHead>
                        <TableHead>Email</TableHead>
                        <TableHead>Position</TableHead>
                        <TableHead class="'text-right'">Salary</TableHead>
                        <TableHead class="'text-center'">Actions</TableHead>
                    </TableRow>
                </TableHeader>
                <TableBody>
                    <TableRow v-for="employee in employees" :key="employee.id">
                        <TableCell class="font-medium">
                            {{ employee.name }}
                        </TableCell>
                        <TableCell>
                            {{ employee.email ?? 'N/A' }}
                        </TableCell>
                        <TableCell class="font-medium">
                            {{ employee.position }}
                        </TableCell>
                        <TableCell class="text-right">
                            {{
                                new Intl.NumberFormat('en-US', {
                                    style: 'currency',
                                    currency: 'USD',
                                }).format(employee.salary)
                            }}
                        </TableCell>
                        <TableCell class="flex justify-center gap-2">
                            <Button as-child size="sm" class="bg-blue-500 text-white hover:bg-blue-700">
                                <Link :href="`/employees/${employee.id}/edit`">
                                    <PencilIcon />
                                </Link>
                            </Button>
                            <Button size="sm" class="bg-rose-500 text-white hover:bg-rose-700" @click="deleteEmploye(employee.id)">
                                <Trash />
                            </Button>
                        </TableCell>
                    </TableRow>
                </TableBody>
            </Table>
        </div>
    </AppLayout>
</template>
