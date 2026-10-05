<script lang="ts">
import { computed, ref } from "vue";
import productsData from "../store/product.json";

type ScrapedProduct = {
    img: string;
    name: string;
    precio: string;
    categoria: string;
    marca: string;
};

type Product = {
    id: number;
    name: string;
    price: number;
    category: string;
    brand: string;
    img: string;
    stock: number;
};

const toPrice = (value: string) => Number(value.replace(/\./g, ""));

const toProduct = (item: ScrapedProduct, index: number) => ({
    id: index + 1,
    name: item.name,
    price: toPrice(item.precio),
    category: item.categoria,
    brand: item.marca,
    img: item.img,
    stock: 10,
});

const initialProducts = Array.isArray(productsData)
    ? (productsData as ScrapedProduct[]).map(toProduct)
    : [];

const products = ref<Product[]>([...initialProducts]);
const loading = ref(false);
const error = ref<string | null>(null);

export function useProductStore() {
    const productCount = computed(() => products.value.length);

    const getAll = () => [...products.value];

    const getById = (id: Product["id"]) =>
        products.value.find((product) => product.id === id) ?? null;

    const setProducts = (items: Product[]) => {
        products.value = [...items];
    };

    const resetProducts = () => {
        products.value = [...initialProducts];
    };

    const addProduct = (product: Product) => {
        const exists = products.value.some((item) => item.id === product.id);
        if (!exists) products.value.push(product);
    };

    const updateProduct = (id: Product["id"], payload: Partial<Product>) => {
        products.value = products.value.map((product) =>
            product.id === id ? { ...product, ...payload } : product,
        );
    };

    const removeProduct = (id: Product["id"]) => {
        products.value = products.value.filter((product) => product.id !== id);
    };

    const clearProducts = () => {
        products.value = [];
    };

    const setLoading = (value: boolean) => {
        loading.value = value;
    };

    const setError = (message: string | null) => {
        error.value = message;
    };

    return {
        products,
        loading,
        error,
        productCount,
        getAll,
        getById,
        setProducts,
        resetProducts,
        addProduct,
        updateProduct,
        removeProduct,
        clearProducts,
        setLoading,
        setError,
    };
}
</script>
