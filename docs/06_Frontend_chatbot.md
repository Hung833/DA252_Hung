## 📱 PHẦN 2: TRIỂN KHAI FRONTEND (REACT NATIVE)
### 1. Cấu hình Dịch vụ Gọi API (API Service)
* **File:** client/src/services/chatApi.ts
* **Nhiệm vụ:** Định nghĩa hàm gửi tin nhắn lên Backend.
  
```TypeScript
import { apiClient } from './api';

export const sendChatMessage = async (message: string) => {
  try {
    const response = await apiClient.post('/chat', { question: message });
    return response.data.answer; // Lấy câu trả lời của AI từ Backend
  } catch (error) {
    console.error("Lỗi khi gọi AI:", error);
    return "Xin lỗi, hệ thống AI đang bận. Vui lòng thử lại sau.";
  }
};
```
### 2. Thiết kế Giao diện Chatbot (Chat UI)
* **File:** client/src/screens/ChatbotScreen.jsx
* **Nhiệm vụ:** Tạo giao diện nhắn tin (tương tự Messenger/Zalo) và quản lý State của lịch sử trò chuyện.
* **💡 Cấu trúc Logic tham khảo:**

  ```JavaScript
  import React, { useState } from 'react';
import { View, Text, TextInput, TouchableOpacity, ScrollView } from 'react-native';
import { sendChatMessage } from '../services/chatApi';

const ChatbotScreen = () => {
  // Quản lý lịch sử tin nhắn
  const [messages, setMessages] = useState([
    { id: 1, sender: 'bot', text: 'Chào bạn! Mình là Trợ lý AI. Bạn cần tư vấn chọn động cơ hay tra thông số nào?' }
  ]);
  const [inputText, setInputText] = useState('');

  const handleSend = async () => {
    if (!inputText.trim()) return;

    // 1. In tin nhắn của User ra màn hình
    const userMsg = { id: Date.now(), sender: 'user', text: inputText };
    setMessages((prev) => [...prev, userMsg]);
    setInputText('');

    // 2. Gọi API Backend
    const botReplyText = await sendChatMessage(inputText);

    // 3. In tin nhắn của Bot ra màn hình
    const botMsg = { id: Date.now() + 1, sender: 'bot', text: botReplyText };
    setMessages((prev) => [...prev, botMsg]);
  };

  return (
    <View style="{{" flex: 1 }}>
      
      <ScrollView>
        {messages.map((msg) => (
          <View key="{msg.id}" style="{{" alignSelf: msg.sender="==" 'user' ? 'flex-end' : 'flex-start' }}>
            <Text>{msg.text}</Text>
          </View>
        ))}
      </ScrollView>

      
      <View style="{{" flexDirection: 'row' }}>
        <TextInput value="{inputText}" onChangeText="{setInputText}" placeholder="Nhập câu hỏi cơ khí..."/>
        <TouchableOpacity onPress="{handleSend}">
          <Text>Gửi</Text>
        </TouchableOpacity>
      </View>
    </View>
  );
};

export default ChatbotScreen;
```
