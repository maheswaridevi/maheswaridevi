//commands 
npx create-react-app crypto-dashboard
cd crypto-dashboard
npm install redux react-redux react-grid-layout recharts axios react-beautiful-dnd

// Redux for state management
import { configureStore } from '@reduxjs/toolkit';
import cryptoReducer from './cryptoSlice';

export const store = configureStore({
  reducer: {
    crypto: cryptoReducer,
  },
});

// src/redux/cryptoSliece.js

import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

const API_URL = "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd";

export const fetchCryptoData = createAsyncThunk('crypto/fetchData', async () => {
  const response = await axios.get(API_URL);
  return response.data;
});

const cryptoSlice = createSlice({
  name: 'crypto',
  initialState: {
    data: [],
    status: 'idle',
  },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchCryptoData.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(fetchCryptoData.fulfilled, (state, action) => {
        state.status = 'succeeded';
        state.data = action.payload;
      })
      .addCase(fetchCryptoData.rejected, (state) => {
        state.status = 'failed';
      });
  },
});

export default cryptoSlice.reducer;

// Dashboard Layout

import React, { useEffect, useState } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchCryptoData } from '../redux/cryptoSlice';
import GridLayout from 'react-grid-layout';
import TableComponent from './TableComponent';
import ChartComponent from './ChartComponent';
import SummaryCard from './SummaryCard';
import 'react-grid-layout/css/styles.css';
import 'react-resizable/css/styles.css';

const Dashboard = () => {
  const dispatch = useDispatch();
  const { data, status } = useSelector((state) => state.crypto);
  const [layout, setLayout] = useState([]);

  useEffect(() => {
    dispatch(fetchCryptoData());
    const interval = setInterval(() => {
      dispatch(fetchCryptoData());
    }, 5000);

    return () => clearInterval(interval);
  }, [dispatch]);

  const saveLayout = () => {
    localStorage.setItem('dashboardLayout', JSON.stringify(layout));
  };

  const loadLayout = () => {
    const savedLayout = localStorage.getItem('dashboardLayout');
    if (savedLayout) setLayout(JSON.parse(savedLayout));
  };

  const layoutItems = [
    { i: 'table', x: 0, y: 0, w: 6, h: 4 },
    { i: 'chart', x: 6, y: 0, w: 6, h: 4 },
    { i: 'summary', x: 0, y: 4, w: 12, h: 2 },
  ];

  return (
    <div>
      <button onClick={saveLayout}>Save Layout</button>
      <button onClick={loadLayout}>Load Layout</button>
      <GridLayout
        className="layout"
        layout={layout.length ? layout : layoutItems}
        cols={12}
        rowHeight={30}
        width={1200}
        onLayoutChange={(currentLayout) => setLayout(currentLayout)}
      >
        <div key="table">
          <TableComponent data={data} />
        </div>
        <div key="chart">
          <ChartComponent data={data} />
        </div>
        <div key="summary">
          <SummaryCard data={data} />
        </div>
      </GridLayout>
    </div>
  );
};

export default Dashboard;

//components creation

import React from 'react';

const TableComponent = ({ data }) => (
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>Price</th>
        <th>Market Cap</th>
      </tr>
    </thead>
    <tbody>
      {data.map((coin) => (
        <tr key={coin.id}>
          <td>{coin.name}</td>
          <td>${coin.current_price}</td>
          <td>${coin.market_cap}</td>
        </tr>
      ))}
    </tbody>
  </table>
);

export default TableComponent;

//chart comonents

import React from 'react';
import { LineChart, Line, CartesianGrid, XAxis, YAxis, Tooltip } from 'recharts';

const ChartComponent = ({ data }) => {
  const chartData = data.map((coin) => ({
    name: coin.name,
    price: coin.current_price,
  }));

  return (
    <LineChart width={600} height={300} data={chartData}>
      <Line type="monotone" dataKey="price" stroke="#8884d8" />
      <CartesianGrid stroke="#ccc" />
      <XAxis dataKey="name" />
      <YAxis />
      <Tooltip />
    </LineChart>
  );
};

export default ChartComponent;

//summary card

import React from 'react';

const SummaryCard = ({ data }) => {
  const highestPrice = Math.max(...data.map((coin) => coin.current_price));
  const lowestPrice = Math.min(...data.map((coin) => coin.current_price));

  return (
    <div>
      <h3>Summary</h3>
      <p>Highest Price: ${highestPrice}</p>
      <p>Lowest Price: ${lowestPrice}</p>
    </div>
  );
};

export default SummaryCard;

// connect redux

import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { store } from './redux/store';
import Dashboard from './components/Dashboard';

ReactDOM.render(
  <Provider store={store}>
    <Dashboard />
  </Provider>,
  document.getElementById('root')
);
