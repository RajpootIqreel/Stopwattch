# Stopwattch
import React, { useState, useEffect, useRef } from "react";
import { StyleSheet, Text, View, Button } from "react-native";

export default function App() {
  // State variables for time
  const [time, setTime] = useState(0); // Time in milliseconds
  const [isRunning, setIsRunning] = useState(false); // Running state

  // useRef to keep track of interval ID without triggering re-renders
  const intervalRef = useRef(null);

  // useEffect to manage the timer lifecycle
  useEffect(() => {
    if (isRunning) {
      // Start the timer
      intervalRef.current = setInterval(() => {
        setTime((prevTime) => prevTime + 10); // Increase by 10ms (100Hz)
      }, 10);
    } else if (!isRunning && intervalRef.current) {
      // Stop the timer when it's not running
      clearInterval(intervalRef.current);
    }

    // Cleanup the interval when component unmounts or the timer is stopped
    return () => clearInterval(intervalRef.current);
  }, [isRunning]);

  // Start/Stop the stopwatch
  const handleStartStop = () => {
    setIsRunning((prevRunning) => !prevRunning);
  };

  // Reset the stopwatch
  const handleReset = () => {
    clearInterval(intervalRef.current); // Clear the interval
    setIsRunning(false); // Stop the timer
    setTime(0); // Reset the time
  };

  // Format time into minutes, seconds, and milliseconds
  const formatTime = (time) => {
    const minutes = Math.floor(time / 60000);
    const seconds = Math.floor((time % 60000) / 1000);
    const milliseconds = Math.floor((time % 1000) / 10);

    return `${minutes < 10 ? "0" : ""}${minutes}:${
      seconds < 10 ? "0" : ""
    }${seconds}:${milliseconds < 10 ? "0" : ""}${milliseconds}`;
  };

  return (
    <View style={styles.container}>
      <Text style={styles.timer}>{formatTime(time)}</Text>
      <View style={styles.buttonContainer}>
        <Button
          title={isRunning ? "Stop" : "Start"}
          onPress={handleStartStop}
        />
        <Button title="Reset" onPress={handleReset} />
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center",
  },
  timer: {
    fontSize: 48,
    fontWeight: "bold",
    marginBottom: 20,
  },
  buttonContainer: {
    flexDirection: "row",
    justifyContent: "space-between",
    width: "50%",
  },
});
