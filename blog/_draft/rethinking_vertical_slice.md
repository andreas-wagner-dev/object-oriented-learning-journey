# Revisit -  Vertical Slice

```
src/
├─ CarRental.Application/             # ➡️ APPLICATION (Composition Root)
│   ├─ CarRentalApp.cs
│   ├─ CarRentalDI.cs
│   ├─ CarRentalSettings.json
│   └─ CarRentalQueueConfig.cs
│
├─ CarRental.Booking/                 # ➡️ USER (Booking)
│   ├─ Reservation/
│   │   ├─ ServedBooking.cs
│   │   ├─ BookingRequest.cs
│   │   ├─ BookingResponse.cs
│   │   ├─ PersistentReservation.cs
│   │   └─ PersistentReservations.cs
│   ├─ Cancellation/
│   │   └─ ServedCancellation.cs
│   ├─ IBookingSession.cs
│   ├─ IReservation.cs
│   └─ IReservations.cs
│
├─ CarRental.CarPool/                 # ➡️ CAR POOL (Inventory)
│   ├─ CarFleet/
│   │   ├─ ServedCarPool.cs
│   │   ├─ CarRequest.cs
│   │   └─ CarResponse.cs
│   ├─ Maintenance/
│   │   ├─ LoggedCar.cs
│   │   ├─ ValidCar.cs
│   │   ├─ PublishedCar.cs
│   │   ├─ ReceivedCar.cs
│   │   └─ CarInMaintenance.cs
│   ├─ ICar.cs
│   ├─ ICarPool.cs
│   ├─ Car.cs
│   ├─ CarStatus.cs
│   ├─ PersistentCar.cs               # Im Root (von Subpaketen genutzt)
│   ├─ PersistentCarPool.cs           # Im Root (von Subpaketen genutzt)
│   ├─ CachedCar.cs                   # Im Root (von Subpaketen genutzt)
│   └─ CachedCarPool.cs               # Im Root (von Subpaketen genutzt)
│
├─ CarRental.Customer/                # ➡️ CUSTOMER (Database)
│   ├─ Registration/                  # 🛠️ Unterpaket: Nur noch Logik & API
│   │  ├─ ServedRegistration.cs
│   │  └─ NotifiedCustomer.cs         # Exklusiver Decorator für Neukunden
│   ├─ ICustomer.cs                   # Abstraktion im Root
│   ├─ ICustomers.cs                  # Abstraktion im Root
│   ├─ Customer.cs                    # Domänen-Basisklasse im Root
│   ├─ PersistentCustomer.cs          # 🔄 IM ROOT (für alle Subpakete)
│   └─ PersistentCustomers.cs         # 🔄 IM ROOT (für alle Subpakete)
│
└─ CarRental.Payment/                # ➡️ PAYMENT (Zahlungsabwicklung)
    ├─ PayPal/
    │   ├─ PayPal.cs
    │   ├─ PayPalRequest.cs
    │   ├─ PayPalResponse.cs
    │   └─ PayPalPayment.cs
    ├─ Stripe/
    │   ├─ Stripe.cs
    │   └─ StripePayment.cs
    ├─ IPayment.cs
    └─ Transaction.cs

```


```csharp
using Microsoft.Extensions.DependencyInjection;

using CarRental.Booking;
using CarRental.CarPool;
using CarRental.Customer;
using CarRental.Payment;

namespace CarRental.Application;

public static class CarRentalDI
{
    public static IServiceCollection 
        AddCarRentalServices(
            this IServiceCollection services)
    {
        // --- 1. BOOKING MODUL ---
        services.AddScoped<
            IReservations, 
            PersistentReservations>();

        // --- 2. CAR POOL MODUL ---
        services.AddScoped<
            PersistentCarPool>();
            
        services.AddScoped<ICarPool>(sp => 
            new CachedCarPool(
                sp.GetRequiredService<
                    PersistentCarPool>()
            ));

        // --- 3. CUSTOMER MODUL ---
        services.AddScoped<
            PersistentCustomers>();
            
        services.AddScoped<ICustomers>(sp => 
            new NotifiedCustomer(
                sp.GetRequiredService<
                    PersistentCustomers>()
            ));

        // --- 4. PAYMENT MODUL ---
        services.AddScoped<IPayment, 
            PayPalPayment>();

        return services;
    }
}


```
