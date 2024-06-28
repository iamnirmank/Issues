## Store Access Token while Logging In
```
const res_token = res.data.body.token.access;
const token = res_token.replace(/^"|"$/g, "");
localStorage.setItem("access_token", token);
```

## Attach Access Token Into the Header and Request 
```
const token = localStorage.getItem("access_token");
const res = await axios.post(
    `${BASE_URL}InventoryApp/inventory/create_inventory/`,
        formData,
        {
          headers: {
            Authorization: `Bearer ${localStorage.getItem("access_token")}`,
          },
        }
    );
```
