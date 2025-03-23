import React, { useState, useEffect } from "react";
import { View, Text, Button, ProgressBar } from "react-native";
import { Card } from "@/components/ui/card";
import { motion } from "framer-motion";
import { Accelerometer } from "expo-sensors";

const FitnessApp = () => {
  const [xp, setXp] = useState(0);
  const [level, setLevel] = useState(1);
  const [mmr, setMmr] = useState(1000);
  const [motionData, setMotionData] = useState(null);
  const [isExercising, setIsExercising] = useState(false);

  useEffect(() => {
    if (xp >= level * 100) {
      setLevel(level + 1);
      setXp(0);
    }
  }, [xp]);

  useEffect(() => {
    let subscription;
    const startTracking = async () => {
      subscription = Accelerometer.addListener((data) => {
        setMotionData(data);
        if (Math.abs(data.z) > 1.2) {
          setIsExercising(true);
        }
      });
    };

    startTracking();
    return () => subscription && subscription.remove();
  }, []);

  const handleWorkout = () => {
    if (isExercising) {
      setXp(xp + 10);
      setMmr(mmr + 5);
      setIsExercising(false);
    }
  };

  return (
    <View className="p-4 bg-gray-900 min-h-screen flex items-center">
      <Card className="w-full max-w-md p-4 bg-gray-800 text-white rounded-2xl shadow-lg">
        <Text className="text-xl font-bold text-center">Niveau {level}</Text>
        <ProgressBar progress={xp / (level * 100)} color="#FFD700" />
        <Text className="text-center mt-2">XP: {xp} / {level * 100}</Text>
        <Text className="text-center mt-2">Classement MMR: {mmr}</Text>
        <Button title="Faire des pompes" onPress={handleWorkout} color="#4CAF50" disabled={!isExercising} />
      </Card>
    </View>
  );
};

export default FitnessApp;
