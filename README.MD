# This is simple store management project using signal, rxjs, behavior subject

### Step 1: Create the Service

```ts
import { Injectable, signal } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class StorageService {
  private storage = signal<Record<string, any>>({}); // Central storage using signals

  // Save data by key
  save(key: string, value: any): void {
    const currentData = this.storage();
    this.storage.set({ ...currentData, [key]: value });
  }

  // Retrieve data by key
  get(key: string): any {
    return this.storage()[key];
  }

  // Delete data by key
  remove(key: string): void {
    const currentData = this.storage();
    const { [key]: _, ...updatedData } = currentData; // Exclude the key to remove
    this.storage.set(updatedData);
  }

  // Get all stored data (optional)
  getAll(): Record<string, any> {
    return this.storage();
  }
}
```

### Step 2: Use the Service in Components

```ts
import { Component } from "@angular/core";
import { StorageService } from "./storage.service";

@Component({
  selector: "app-root",
  template: `<button (click)="saveData()">Save Data</button>`,
})
export class AppComponent {
  constructor(private storageService: StorageService) {}

  saveData() {
    this.storageService.save("user", { name: "John", age: 30 });
    console.log("Data saved!");
  }
}
```

### Retrieving Data

```ts
import { Component } from "@angular/core";
import { StorageService } from "./storage.service";

@Component({
  selector: "app-display",
  template: `<button (click)="showData()">Show Data</button>`,
})
export class DisplayComponent {
  constructor(private storageService: StorageService) {}

  showData() {
    const userData = this.storageService.get("user");
    console.log("Retrieved Data:", userData);
  }
}
```

# Using RxJS

### Step 1: Create the Service

```ts
import { Injectable } from "@angular/core";
import { BehaviorSubject } from "rxjs";

@Injectable({
  providedIn: "root",
})
export class StorageService {
  private storage = new BehaviorSubject<Record<string, any>>({});

  // Save data by key
  save(key: string, value: any): void {
    const currentData = this.storage.value;
    this.storage.next({ ...currentData, [key]: value });
  }

  // Retrieve data by key
  get(key: string): any {
    return this.storage.value[key];
  }

  // Subscribe to data changes (optional)
  watch(key: string) {
    return this.storage.asObservable();
  }

  // Delete data by key
  remove(key: string): void {
    const currentData = this.storage.value;
    const { [key]: _, ...updatedData } = currentData; // Exclude the key to remove
    this.storage.next(updatedData);
  }
}
```

## Step 2: Use the Service in Components

### Saving Data

```ts
import { Component } from "@angular/core";
import { StorageService } from "./storage.service";

@Component({
  selector: "app-root",
  template: `<button (click)="saveData()">Save Data</button>`,
})
export class AppComponent {
  constructor(private storageService: StorageService) {}

  saveData() {
    this.storageService.save("settings", { theme: "dark", language: "en" });
    console.log("Data saved!");
  }
}
```

### Retrieving Data

```ts
import { Component } from "@angular/core";
import { StorageService } from "./storage.service";

@Component({
  selector: "app-settings",
  template: `<button (click)="showData()">Show Settings</button>`,
})
export class SettingsComponent {
  constructor(private storageService: StorageService) {}

  showData() {
    const settings = this.storageService.get("settings");
    console.log("Retrieved Settings:", settings);
  }
}
```

### Watching Data Changes

```ts
import { Component, OnInit } from "@angular/core";
import { StorageService } from "./storage.service";

@Component({
  selector: "app-watch",
  template: `<p>Check console for updates</p>`,
})
export class WatchComponent implements OnInit {
  constructor(private storageService: StorageService) {}

  ngOnInit() {
    this.storageService.watch("settings").subscribe((data) => {
      console.log("Storage updated:", data);
    });
  }
}
```





## Comparison: Signals vs. RxJS

| Feature                 | Angular Signals                                | RxJS                                             |
|-------------------------|-----------------------------------------------|-------------------------------------------------|
| **Reactivity**          | Simplifies state management for local data.   | Better for managing streams and asynchronous data. |
| **Simplicity**          | Easier to use for small, reactive storage.    | Requires more setup but offers more flexibility. |
| **Performance**         | Built for Angular and optimized for change detection. | Slightly heavier due to Observable subscriptions. |

---
