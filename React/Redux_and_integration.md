## Installation
```
npm install @reduxjs/toolkit
npm i react-redux
```
## urlConfig.tsx
```
export const BASE_URL = "https://restronovabackend.azurewebsites.net/api/";
```
## Store.tsx
```
import { configureStore } from "@reduxjs/toolkit";
import { useDispatch } from "react-redux";
import RootReducer from "./RootReducers";

export const Store = configureStore({
  reducer: RootReducer,
});

export type AppDispatch = typeof Store.dispatch;
export const useAppDispatch = () => useDispatch<AppDispatch>();
```
## Root Reducers.tsx
```
import { combineReducers } from "redux";
import authSlice from "./features/Auth/AuthSlice";

const RootReducer = combineReducers({
  authSlice: authSlice,
});

export default RootReducer;
```
## features/ Auth / AuthApi.tsx
```
import { createAsyncThunk } from "@reduxjs/toolkit";
import axios from "axios";
import { BASE_URL } from "../../../apiConfigs/urlConfig";
import { toast } from "react-toastify";

export const login = createAsyncThunk(
  "login",
  async (newData: LoginType, { dispatch, rejectWithValue }) => {
    try {
      const res = await axios.post(
        `${BASE_URL}AuthApp/api/user/login/`,
        newData
      );
      if (res.data.success) {
        const token = res.data.body.token.replace(/^"|"$/g, "");
        localStorage.setItem("access_token", token);
        localStorage.setItem("user_id", res.data.body.id);
        toast.success(res.data.message);
        // window.location.href = '/';
      } else {
        toast.error(res.data.message);
      }
      return res.data;
    } catch (error: any) {
      if (!error.response) {
        throw error;
      }
      return rejectWithValue(error.response.data);
    }
  }
);
```
## features/ Auth / AuthApi.tsx
```
import { createSlice, PayloadAction } from "@reduxjs/toolkit";
import { login } from "../Auth/AuthApi";
// import { toast } from "react-toastify";
export interface LoginType {
  email: string;
  password: string;
}
interface AuthStates {
  loading: boolean;
  req_success: boolean;
  user: LoginType;
}
const initialState: AuthStates = {
  loading: false,
  req_success: false,
  user: "" as any,
};
const authSlice = createSlice({
  name: "authSlice",
  initialState,
  reducers: {
  //   updateTheme(state, action) {
  //     state.isLight = action.payload;
  //   },
  },

  extraReducers: (builder: any) => {
    builder.addCase(login.pending, (state: any) => {
      state.loading = true;
      state.req_success = false;
    });
    builder.addCase(
      login.fulfilled,
      (state: any, { payload }: PayloadAction<any>) => {
        state.loading = false;
        state.req_success = true;
        localStorage.setItem("user_id", payload.body.user.id);
        localStorage.setItem("outlet", payload.body.user.outlet);
      }
    );
    builder.addCase(login.rejected, (state: AuthStates) => {
      state.loading = false;
    });
    },
});
// export const {updateTheme} = rawMaterialSlice.actions;
export default authSlice.reducer;
```
## Usage of the Api Functions
```
import { useAppDispatch } from "store/store";
const dispatch = useAppDispatch();
dispatch(login(loginData))
```
## Usage of the Slice States
```
import { useSelector } from "react-redux";
const auth = useSelector((state: any) => state.authSlice);
```
Navigating 1 page to another
```
import { useNavigate } from "react-router-dom";
const navigate = useNavigate();
navigate('/path')
```
