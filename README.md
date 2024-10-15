#include <iostream>
#include <string>
#include <map>
#include <vector>
#include <thread>
#include <chrono>
#include <random>
#include <fstream>
#include <sstream>

using namespace std;

// Device Class
class Device {
public:
    string name;
    string type;
    bool state;

    Device(string name, string type, bool state) : name(name), type(type), state(state) {}

    virtual void toggle() {
        state = !state;
        cout << "Device '" << name << "' state toggled to " << (state ? "ON" : "OFF") << endl;
    }

    // Additional functions for device-specific actions can be added here
};

// Light Class (inherits from Device)
class Light : public Device {
public:
    Light(string name, bool state) : Device(name, "light", state) {}

    void setBrightness(int brightness) {
        cout << "Light '" << name << "' brightness set to " << brightness << "%" << endl;
    }

    // Specific actions for lights (e.g., change color, etc.)
};

// Temperature Sensor Class
class TemperatureSensor {
public:
    string sensorId;
    double temperature;

    TemperatureSensor(string sensorId, double temperature) : sensorId(sensorId), temperature(temperature) {}

    double getTemperature() {
        return temperature;
    }
};

// Smart Home System Class
class SmartHomeSystem {
private:
    map<string, Device*> devices;
    vector<TemperatureSensor> sensors;
    random_device rd;
    mt19937 gen;
    uniform_real_distribution<> distrib;

public:
    // Constructor
    SmartHomeSystem() : gen(rd()), distrib(18.0, 25.0) {}

    // Add a device to the system
    void addDevice(Device* device) {
        devices[device->name] = device;
    }

    // Add a temperature sensor
    void addSensor(TemperatureSensor sensor) {
        sensors.push_back(sensor);
    }

    // Get a device by name
    Device* getDevice(const string& name) {
        if (devices.find(name) != devices.end()) {
            return devices[name];
        }
        return nullptr;
    }

    // Simulate temperature readings
    void simulateTemperatureReadings() {
        for (TemperatureSensor& sensor : sensors) {
            sensor.temperature = distrib(gen);
        }
    }

    // Display temperature readings
    void displayTemperatureReadings() {
        cout << "Temperature Readings:" << endl;
        for (const TemperatureSensor& sensor : sensors) {
            cout << "  " << sensor.sensorId << ": " << sensor.temperature << "°C" << endl;
        }
    }

    // Toggle a device


    void toggleDevice(const string& name) {
        Device* device = getDevice(name);
        if (device != nullptr) {
            device->toggle();
        } else {
            cout << "Device not found." << endl;
        }
    }

    // Set light brightness
    void setLightBrightness(const string& name, int brightness) {
        Light* light = dynamic_cast<Light*>(getDevice(name));
        if (light != nullptr) {
            light->setBrightness(brightness);
        } else {
            cout << "Device is not a light." << endl;
        }
    }

    // Simulate smart home actions (not shown here)
    // void simulateActions() {
    //     // Check temperature readings and adjust devices accordingly
    //     // e.g.,
    //     // if (sensor.temperature < 20) {
    //     //     toggleDevice("heater");
    //     // }
    // }
};

int main() {
    SmartHomeSystem system;

    // Add devices
    system.addDevice(new Light("Living Room Light", true));
    system.addDevice(new Device("Kitchen Fan", "fan", false));

    // Add temperature sensors
    system.addSensor(TemperatureSensor("Living Room", 22.0));
    system.addSensor(TemperatureSensor("Bedroom", 21.0));

    // Simulate temperature readings
    system.simulateTemperatureReadings();
    system.displayTemperatureReadings();

    // User interaction (simplified)
    while (true) {
        string command;
        cout << "Enter a command (e.g., 'toggle Living Room Light', 'brightness Living Room Light 50', 'exit'): ";
        getline(cin, command);

        if (command == "exit") {
            break;
        }

        // Example command parsing (basic)
        istringstream iss(command);
        string action, deviceName, brightnessStr;
        iss >> action >> deviceName;
        if (action == "toggle") {
            system.toggleDevice(deviceName);
        } else if (action == "brightness") {
            iss >> brightnessStr;
            int brightness = stoi(brightnessStr);
            system.setLightBrightness(deviceName, brightness);
        } else {
            cout << "Invalid command." << endl;
        }
        
        // Simulate actions (not shown here)
        // system.simulateActions(); 
    }

    return 0;
}
