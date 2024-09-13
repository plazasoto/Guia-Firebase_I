<template>
    <div>
        <form @submit.prevent="addMedicamento">
            <input v-model="nuevoMedicamento" placeholder="Nombre de Medicamento" />
            <button type="submit">Agregar</button>
        </form>
        <ul>
            <li v-for="medicamento in medicamentos" :key="medicamento.id">
                {{ medicamento.id }} - {{ medicamento.medicamento }}
                <button @click="deleteMedicamento(medicamento.id)" >Eliminar</button>
            </li>
        </ul>
    </div>
</template>

<script>
    import { getFirestore, collection, onSnapshot, addDoc, doc, deleteDoc } from 'firebase/firestore';
    import firebaseApp from '../firebaseconfig';
    export default {
        data() {
            return {
                nuevoMedicamento: '',
                medicamentos: []
            };
        },
        mounted() {
            const db = getFirestore(firebaseApp);
            const farmaciaRef = collection(db, 'farmacia' );
            onSnapshot(farmaciaRef, (snapshot) => {
                this.medicamentos = snapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }));
            });
        },
        methods: {
            async addMedicamento() {
                if (this.nuevoMedicamento.trim() === '' ) return;
                const db = getFirestore(firebaseApp);
                const farmaciaRef = collection(db, 'farmacia' );
                await addDoc(farmaciaRef, { medicamento: this.nuevoMedicamento });
                this.nuevoMedicamento = ''; 
            },
            async deleteMedicamento(medicamentoId) {
                const db = getFirestore(firebaseApp);
                const farmaciaRef = doc(db, 'farmacia' , medicamentoId);
                await deleteDoc(farmaciaRef);
            }
        }
    };
</script>