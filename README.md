import React, { useState, useEffect } from 'react';
import { View, Text, TextInput, TouchableOpacity, Alert, StyleSheet, ImageBackground, Animated, Easing } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

export default function App() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [fadeAnim] = useState(new Animated.Value(0));
  const [fallingCoins] = useState(new Animated.Value(0));

  useEffect(() => {
    Animated.timing(fadeAnim, {
      toValue: 1,
      duration: 1000,
      useNativeDriver: true,
    }).start();

    Animated.loop(
      Animated.timing(fallingCoins, {
        toValue: 1,
        duration: 3000,
        easing: Easing.linear,
        useNativeDriver: true,
      })
    ).start();
  }, [fadeAnim, fallingCoins]);

  const handleRegister = async () => {
    if (!email || !password) {
      Alert.alert('Erro', 'Preenche todos os campos!');
      return;
    }
    await AsyncStorage.setItem('userEmail', email);
    await AsyncStorage.setItem('userPassword', password);
    Alert.alert('Sucesso', 'Conta criada com sucesso! Agora faz login.');
  };

  const handleLogin = async () => {
    const savedEmail = await AsyncStorage.getItem('userEmail');
    const savedPassword = await AsyncStorage.getItem('userPassword');

    if (email === savedEmail && password === savedPassword) {
      Alert.alert('Sucesso', 'Bem-vindo de volta à POUPAR+!');
    } else {
      Alert.alert('Erro', 'E-mail ou palavra-passe incorretos.');
    }
  };

  return (
    <ImageBackground
      source={{ uri: 'https://cdn.pixabay.com/photo/2020/01/15/07/39/money-4761531_1280.jpg' }}
      style={styles.background}
      resizeMode="cover"
    >
      <View style={styles.overlay}>
        <Animated.Text style={[styles.title, { opacity: fadeAnim }]}>💰 POUPAR+ 💰</Animated.Text>

        <Animated.Image
          source={{ uri: 'https://cdn-icons-png.flaticon.com/512/860/860840.png' }}
          style={[styles.coin, { transform: [{ translateY: fallingCoins.interpolate({ inputRange: [0, 1], outputRange: [-50, 300] }) }] }]}
        />

        <TextInput
          style={styles.input}
          placeholder="E-mail"
          placeholderTextColor="#aaa"
          keyboardType="email-address"
          autoCapitalize="none"
          onChangeText={setEmail}
        />

        <TextInput
          style={styles.input}
          placeholder="Palavra-passe"
          placeholderTextColor="#aaa"
          secureTextEntry
          onChangeText={setPassword}
        />

        <TouchableOpacity style={styles.buttonPrimary} onPress={handleRegister}>
          <Text style={styles.buttonText}>Criar Conta</Text>
        </TouchableOpacity>

        <TouchableOpacity style={styles.buttonSecondary} onPress={handleLogin}>
          <Text style={styles.buttonText}>Entrar</Text>
        </TouchableOpacity>
      </View>
    </ImageBackground>
  );
}

const styles = StyleSheet.create({
  background: {
    flex: 1,
    width: '100%',
    height: '100%',
  },
  overlay: {
    flex: 1,
    backgroundColor: 'rgba(0, 0, 0, 0.6)',
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  title: {
    fontSize: 28,
    fontWeight: 'bold',
    color: '#fff',
    marginBottom: 30,
  },
  input: {
    width: '90%',
    height: 50,
    borderWidth: 1,
    borderColor: '#ccc',
    borderRadius: 12,
    paddingHorizontal: 15,
    backgroundColor: '#fff',
    fontSize: 16,
    color: '#333',
    marginBottom: 15,
  },
  buttonPrimary: {
    width: '90%',
    backgroundColor: '#007bff',
    padding: 15,
    borderRadius: 12,
    alignItems: 'center',
    marginBottom: 10,
  },
  buttonSecondary: {
    width: '90%',
    backgroundColor: '#28a745',
    padding: 15,
    borderRadius: 12,
    alignItems: 'center',
  },
  buttonText: {
    color: '#fff',
    fontSize: 16,
    fontWeight: 'bold',
  },
  coin: {
    width: 50,
    height: 50,
    position: 'absolute',
    top: -50,
  },
});
