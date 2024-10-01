Part 2 Changes
Menu Management Feature Added:

Implemented the functionality to add, view, and remove menu items.
Included average price calculation for menu items.
Enhanced user interface with better design and improved layout for the menu management screen.
Dish Filtering Feature:

Added the option to filter dishes by course type (starter, main, dessert).
Error handling implemented for when no matching course is found.
Refined Input Fields:

Updated input fields with error handling for invalid or empty inputs.
Improved keyboard types for specific input fields like price (numeric).
Refactoring Changes
Removed Login/Signup Feature:

Refactored the app to remove the login and signup screens for a smoother user experience.
Adjusted navigation flow accordingly.
Improved Code Structure:

Reorganized code for better readability and maintainability.
Extracted common components such as the button into reusable components.
Visual Enhancements:

my code:

import React, { useState } from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import { View, Text, TextInput, TouchableOpacity, FlatList, StyleSheet, StatusBar, Alert } from 'react-native';

// Custom Button Component with Touch Feedback
function CustomButton({ onPress, title }) {
  return (
    <TouchableOpacity style={styles.customButton} onPress={onPress} activeOpacity={0.7}>
      <Text style={styles.buttonText}>{title}</Text>
    </TouchableOpacity>
  );
}

// Screen for Login
function LoginScreen({ navigation }) {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = () => {
    navigation.navigate('Home');
  };

  return (
    <View style={styles.container}>
      <StatusBar barStyle="light-content" />
      <Text style={styles.title}>Welcome to Munchies Palace</Text>
      <TextInput
        style={styles.input}
        value={username}
        onChangeText={setUsername}
        placeholder="Enter your username"
        placeholderTextColor="#ccc"
      />
      <TextInput
        style={styles.input}
        value={password}
        onChangeText={setPassword}
        secureTextEntry
        placeholder="Enter your password"
        placeholderTextColor="#ccc"
      />
      <CustomButton title="Login" onPress={handleLogin} />
      <TouchableOpacity onPress={() => navigation.navigate('SignUp')}>
        <Text style={styles.link}>Create New Account</Text>
      </TouchableOpacity>
    </View>
  );
}

// Screen for Sign Up
function SignUpScreen({ navigation }) {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSignUp = () => {
    navigation.navigate('Home');
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Sign Up</Text>
      <TextInput
        style={styles.input}
        value={name}
        onChangeText={setName}
        placeholder="Enter your name"
        placeholderTextColor="#ccc"
      />
      <TextInput
        style={styles.input}
        value={email}
        onChangeText={setEmail}
        placeholder="Enter your email"
        placeholderTextColor="#ccc"
      />
      <TextInput
        style={styles.input}
        value={password}
        onChangeText={setPassword}
        secureTextEntry
        placeholder="Enter your password"
        placeholderTextColor="#ccc"
      />
      <CustomButton title="Sign Up" onPress={handleSignUp} />
    </View>
  );
}

// Screen for Home
function HomeScreen({ navigation }) {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Home</Text>
      <CustomButton title="Add/Remove Menu" onPress={() => navigation.navigate('MenuManagement')} />
      <CustomButton title="Filter Menu" onPress={() => navigation.navigate('FilterMenu')} />
    </View>
  );
}

// Screen for Menu Management
function MenuManagementScreen({ navigation }) {
  const [menuItems, setMenuItems] = useState([]);

  const handleRemoveDish = (id) => {
    setMenuItems(menuItems.filter(item => item.id !== id));
  };

  const calculateAveragePrice = (items) => {
    if (items.length === 0) return 0;
    const total = items.reduce((sum, item) => sum + item.price, 0);
    return (total / items.length).toFixed(2);
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Menu Management</Text>
      <CustomButton title="Add New Dish" onPress={() => navigation.navigate('AddDish', { setMenuItems, menuItems })} />
      <FlatList
        data={menuItems}
        keyExtractor={item => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.menuItem}>
            <Text style={styles.menuItemText}>{item.name} - ${item.price.toFixed(2)}</Text>
            <CustomButton title="Remove" onPress={() => handleRemoveDish(item.id)} />
          </View>
        )}
      />
      <Text style={styles.text}>Total Items: {menuItems.length}</Text>
      <Text style={styles.text}>Average Price per Dish: ${calculateAveragePrice(menuItems)}</Text>
    </View>
  );
}

// Screen for Add Dish
function AddDishScreen({ route, navigation }) {
  const { setMenuItems, menuItems } = route.params;
  const [name, setName] = useState('');
  const [price, setPrice] = useState('');
  const [course, setCourse] = useState('');

  const handleSaveDish = () => {
    const parsedPrice = parseFloat(price);
    if (isNaN(parsedPrice)) {
      Alert.alert('Error', 'Please enter a valid price.');
      return;
    }

    const newDish = {
      id: menuItems.length + 1,
      name,
      price: parsedPrice,
      course,
    };
    setMenuItems([...menuItems, newDish]);
    navigation.goBack();
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Add New Dish</Text>
      <TextInput
        style={styles.input}
        value={name}
        onChangeText={setName}
        placeholder="Dish Name"
        placeholderTextColor="#ccc"
      />
      <TextInput
        style={styles.input}
        value={price}
        onChangeText={setPrice}
        placeholder="Dish Price"
        keyboardType="numeric"
        placeholderTextColor="#ccc"
      />
      <TextInput
        style={styles.input}
        value={course}
        onChangeText={setCourse}
        placeholder="Course (starter, main, dessert)"
        placeholderTextColor="#ccc"
      />
      <CustomButton title="Save Dish" onPress={handleSaveDish} />
    </View>
  );
}

// Screen for Filter Menu
function FilterMenuScreen({ route }) {
  const [filteredItems, setFilteredItems] = useState([]);
  const [error, setError] = useState('');

  const { menuItems } = route.params;

  // Handle the filtering logic
  const handleFilter = (course) => {
    const filtered = menuItems.filter(item => item.course.toLowerCase() === course.toLowerCase());
    if (filtered.length === 0) {
      setError(No dishes found for "${course}".);
      setFilteredItems([]);
    } else {
      setFilteredItems(filtered);
      setError('');
    }
  };

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Filter Menu by Course</Text>

      {/* Buttons for each course */}
      <View style={styles.filterButtonContainer}>
        <TouchableOpacity style={styles.filterButton} onPress={() => handleFilter('Starters')}>
          <Text style={styles.filterButtonText}>Starters</Text>
        </TouchableOpacity>
        <TouchableOpacity style={styles.filterButton} onPress={() => handleFilter('Mains')}>
          <Text style={styles.filterButtonText}>Mains</Text>
        </TouchableOpacity>
        <TouchableOpacity style={styles.filterButton} onPress={() => handleFilter('Desserts')}>
          <Text style={styles.filterButtonText}>Desserts</Text>
        </TouchableOpacity>
      </View>

      {/* Error message if no items are found */}
      {error ? <Text style={styles.errorText}>{error}</Text> : null}

      {/* Display filtered items */}
      <FlatList
        data={filteredItems}
        keyExtractor={item => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.menuItem}>
            <Text style={styles.menuItemText}>{item.name} - ${item.price.toFixed(2)}</Text>
          </View>
        )}
      />
    </View>
  );
}

// Stack Navigator Setup
const Stack = createStackNavigator();

export default function App() {
  const [menuItems, setMenuItems] = useState([]);

  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Login">
        <Stack.Screen name="Login" component={LoginScreen} />
        <Stack.Screen name="SignUp" component={SignUpScreen} />
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen
          name="MenuManagement"
          component={MenuManagementScreen}
          initialParams={{ menuItems, setMenuItems }}
        />
        <Stack.Screen
          name="AddDish"
          component={AddDishScreen}
          initialParams={{ menuItems, setMenuItems }}
        />
        <Stack.Screen
          name="FilterMenu"
          component={FilterMenuScreen}
          initialParams={{ menuItems }}
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

// Stylesheet
const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    justifyContent: 'center',
    backgroundColor: '#1e1e1e',
  },
  input: {
    height: 45,
    borderColor: '#444',
    borderWidth: 1,
    marginBottom: 15,
    color: '#fff',
    paddingHorizontal: 10,
    borderRadius: 8,
    backgroundColor: '#333',
  },
  customButton: {
    backgroundColor: '#6200ee',
    paddingVertical: 12,
    paddingHorizontal: 20,
    borderRadius: 8,
    marginBottom: 15,
  },
  buttonText: {
    color: '#fff',
    fontSize: 16,
    textAlign: 'center',
  },
  title: {
    fontSize: 24,
    color: '#fff',
    textAlign: 'center',
    marginBottom: 20,
  },
  link: {
    color: '#03dac6',
    textAlign: 'center',
    marginTop: 10,
  },
  filterButtonContainer: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    marginBottom: 20,
  },
  filterButton: {
    backgroundColor: '#6200ee',
    paddingVertical: 10,
    paddingHorizontal: 20,
    borderRadius: 5,
  },
  filterButtonText: {
    color: '#fff',
    fontSize: 16,
  },
  errorText: {
    color: 'red',
    textAlign: 'center',
    marginTop: 10,
    fontSize: 16,
  },
  menuItem: {
    backgroundColor: '#333',
    padding: 15,
    marginBottom: 10,
    borderRadius: 8,
  },
  menuItemText: {
    color: '#fff',
    fontSize: 18,
  },
  text: {
    color: '#fff',
    fontSize: 16,
    marginTop: 10,
    textAlign: 'center',
  },
});

Introduced hover effects and custom styling to buttons and menu items for better user experience.
Added a light/dark mode toggle to enhance the design.
Bug Fixes:

Fixed the issue where price was showing as NaN when adding new dishes.
Corrected the filter functionality to handle non-matching courses gracefully.
