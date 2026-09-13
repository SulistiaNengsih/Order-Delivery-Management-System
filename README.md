# Order Delivery Management System

A delivery-management system for micro, small and medium enterprises in Indonesia. Customers place and track
orders; couriers and admins manage products, orders and deliveries from an Android app.

The system is split across two repositories:

| Part | Repository | Stack |
|---|---|---|
| Backend REST API | [ManajemenLayananPesanAntar](https://github.com/SulistiaNengsih/ManajemenLayananPesanAntar) | ASP.NET Core 8, EF Core, MySQL, JWT, Swagger |
| Android app | [Manajemen-Layanan-Pesan-Antar-Android-App](https://github.com/SulistiaNengsih/Manajemen-Layanan-Pesan-Antar-Android-App) | Kotlin, Jetpack Compose, Retrofit, Google Maps |

## How the two fit together

```
Android app  ──HTTP/JSON (Retrofit)──▶  ASP.NET Core API  ──EF Core──▶  MySQL
     ▲                                          │
     └──────── push notification ───────────────┘
                (Firebase Cloud Messaging)
```

The app authenticates against the API with a JWT, calls the order endpoints, and posts the courier's location
while a delivery is in progress. The API stores device tokens and pushes delivery status changes back to the
app through Firebase.

## Order lifecycle

The API owns the order state machine. Status changes happen through explicit transition endpoints rather than
the client setting a status directly:

```
Temporer ──▶ Dalam_Proses ──▶ Dalam_Pengiriman ──▶ Diterima
   (draft)     (processing)     (out for delivery)   (received)

                    ├──▶ Tertunda    (suspended, with a reason)
                    └──▶ Dibatalkan  (cancelled, with a reason)
```

Roles are `Admin` and `Kurir` (courier).

## Running it

Start with the API. Setup instructions, the full endpoint table and the architecture notes are in the
[backend README](https://github.com/SulistiaNengsih/ManajemenLayananPesanAntar#readme). Point the Android
app's base URL at the running API.
