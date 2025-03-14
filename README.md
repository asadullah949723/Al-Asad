# Al-Asad
import React, { useState, useEffect } from "react";
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import { getAuth, signInWithPopup, GoogleAuthProvider, signOut } from "firebase/auth";
import { getFirestore, collection, getDocs } from "firebase/firestore";
import { app } from "./firebase";

const auth = getAuth(app);
const db = getFirestore(app);

const Home = () => {
  return (
    <div className="min-h-screen bg-gray-100 flex flex-col items-center p-6">
      <header className="w-full max-w-6xl bg-white shadow-lg rounded-2xl p-6 flex justify-between items-center">
        <h1 className="text-2xl font-bold text-gray-800">Al-Asad Foods Limited</h1>
        <nav>
          <Link to="/" className="text-gray-600 hover:text-gray-800 mx-4">Home</Link>
          <Link to="/shop" className="text-gray-600 hover:text-gray-800 mx-4">Shop</Link>
          <Link to="/login" className="text-gray-600 hover:text-gray-800 mx-4">Login</Link>
        </nav>
      </header>
      
      <main className="w-full max-w-6xl mt-8">
        <div className="bg-white shadow-lg rounded-2xl p-6 text-center">
          <h2 className="text-3xl font-semibold text-gray-800">Wholesale Grocery Delivery</h2>
          <p className="text-gray-600 mt-2">Supplying restaurants, caterers, supermarkets & butchers in London</p>
          <Link to="/shop" className="mt-4 inline-block px-6 py-3 bg-blue-600 text-white rounded-xl hover:bg-blue-700">Shop Now</Link>
        </div>
      </main>
    </div>
  );
};

const Shop = () => {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    const fetchProducts = async () => {
      const querySnapshot = await getDocs(collection(db, "products"));
      setProducts(querySnapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
    };
    fetchProducts();
  }, []);

  return (
    <div className="p-6">
      <h2 className="text-2xl font-bold">Shop</h2>
      <div className="grid grid-cols-3 gap-4 mt-4">
        {products.map(product => (
          <div key={product.id} className="bg-white p-4 shadow-lg rounded-lg">
            <h3 className="text-lg font-semibold">{product.name}</h3>
            <p className="text-gray-600">£{product.price}</p>
          </div>
        ))}
      </div>
    </div>
  );
};

const Login = () => {
  const handleLogin = async () => {
    const provider = new GoogleAuthProvider();
    await signInWithPopup(auth, provider);
  };

  const handleLogout = async () => {
    await signOut(auth);
  };

  return (
    <div className="p-6 text-center">
      <h2 className="text-2xl font-bold">Login</h2>
      <button onClick={handleLogin} className="mt-4 px-6 py-3 bg-green-600 text-white rounded-xl hover:bg-green-700">Sign in with Google</button>
      <button onClick={handleLogout} className="mt-4 px-6 py-3 bg-red-600 text-white rounded-xl hover:bg-red-700">Logout</button>
    </div>
  );
};

const App = () => {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/shop" element={<Shop />} />
        <Route path="/login" element={<Login />} />
      </Routes>
    </Router>
  );
};

export default App;
