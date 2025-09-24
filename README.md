import 'package:flutter/material.dart';
import 'dart:async';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Rocket Launch Controller',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const RocketLaunchController(),
    );
  }
}

class RocketLaunchController extends StatefulWidget {
  const RocketLaunchController({super.key});

  @override
  _RocketLaunchControllerState createState() => _RocketLaunchControllerState();
}

class _RocketLaunchControllerState extends State<RocketLaunchController> {
  int _counter = 0;
  Timer? _countdownTimer;
  String _message = 'Launch Control Ready';

  Color _getTextColor() {
    if (_counter == 0) {
      return Colors.red;
    } else if (_counter > 50 && _counter < 100) {
      return Colors.green;
    } else if (_counter >= 1 && _counter <= 50) {
      return Colors.orange;
    } else if (_counter == 100) {
      return Colors.blue; 
    }
    return Colors.black; 
  }

  void _igniteRocket() {
    if (_counter < 100) {
      setState(() {
        _counter += 1;
        _updateMessage();
      });
    }
    if (_counter == 100) {
      _startCountdown();
    }
  }
  void _decrementCounter() {
    if (_counter > 0) {
      setState(() {
        _counter -= 1;
        _updateMessage();
      });
    }
  }

  void _resetCounter() {
    if (_countdownTimer != null) {
      _countdownTimer!.cancel();
    }
    setState(() {
      _counter = 0;
      _message = 'Launch Control Ready';
    });
  }

  void _updateMessage() {
    if (_counter < 100) {
      _message = 'Fueling...';
    } else if (_counter == 100) {
      _message = 'Countdown Initiated!';
    }
  }

  void _startCountdown() {
    setState(() {
      _message = 'LIFTOFF!';
    });
    WidgetsBinding.instance.addPostFrameCallback((_) {
      _showLaunchSuccessDialog();
    });
  }

  void _showLaunchSuccessDialog() {
    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(20.0),
          ),
          backgroundColor: Colors.blue[900],
          content: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              Icon(
                Icons.rocket_launch,
                size: 80.0,
                color: Colors.white,
              ),
              const SizedBox(height: 20),
              Text(
                'LIFTOFF!',
                style: TextStyle(
                  fontSize: 28,
                  fontWeight: FontWeight.bold,
                  color: Colors.yellow[400],
                ),
              ),
              const SizedBox(height: 10),
              Text(
                'Mission Successful! Congratulations, cadet!',
                textAlign: TextAlign.center,
                style: TextStyle(
                  fontSize: 16,
                  color: Colors.white,
                ),
              ),
            ],
          ),
          actions: [
            TextButton(
              onPressed: () {
                Navigator.of(context).pop();
                _resetCounter();
              },
              child: const Text(
                'OK',
                style: TextStyle(color: Colors.white),
              ),
            ),
          ],
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Rocket Launch Controller'),
        centerTitle: true,
      ),
      body: Container(
        decoration: BoxDecoration(
          gradient: LinearGradient(
            begin: Alignment.topCenter,
            end: Alignment.bottomCenter,
            colors: [
              Colors.blue[900]!,
              Colors.black,
            ],
          ),
        ),
        child: Padding(
          padding: const EdgeInsets.all(16.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: [
              Center(
                child: Text(
                  '$_counter',
                  style: TextStyle(
                    fontSize: 100.0,
                    fontWeight: FontWeight.bold,
                    color: _getTextColor(),
                    shadows: [
                      Shadow(
                        blurRadius: 10.0,
                        color: _getTextColor(),
                        offset: const Offset(0, 0),
                      ),
                    ],
                  ),
                ),
              ),
              const SizedBox(height: 16),
              Center(
                child: Text(
                  _message,
                  style: TextStyle(
                    fontSize: 24,
                    color: Colors.white,
                    fontWeight: FontWeight.w500,
                  ),
                ),
              ),
              const SizedBox(height: 32),
              SliderTheme(
                data: SliderTheme.of(context).copyWith(
                  thumbShape: RoundSliderThumbShape(enabledThumbRadius: 15.0),
                  overlayShape: RoundSliderOverlayShape(overlayRadius: 30.0),
                  thumbColor: Colors.amber,
                  activeTrackColor: Colors.blue[300],
                  inactiveTrackColor: Colors.grey[800],
                ),
                child: Slider(
                  min: 0,
                  max: 100,
                  value: _counter.toDouble(),
                  onChanged: (double value) {
                    setState(() {
                      _counter = value.toInt();
                      _updateMessage();
                    });
                  },
                ),
              ),
              const SizedBox(height: 32),
              Row(
                mainAxisAlignment: MainAxisAlignment.spaceEvenly,
                children: [
                  _buildButton(
                    onPressed: _igniteRocket,
                    label: 'Ignite',
                    icon: Icons.local_fire_department,
                    color: Colors.redAccent,
                  ),
                  _buildButton(
                    onPressed: _decrementCounter,
                    label: 'Decrement',
                    icon: Icons.remove,
                    color: Colors.deepPurple,
                  ),
                ],
              ),
              const SizedBox(height: 16),
              _buildButton(
                onPressed: _resetCounter,
                label: 'Reset',
                icon: Icons.refresh,
                color: Colors.teal,
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildButton({
    required VoidCallback onPressed,
    required String label,
    required IconData icon,
    required Color color,
  }) {
    return Expanded(
      child: Padding(
        padding: const EdgeInsets.symmetric(horizontal: 8.0),
        child: ElevatedButton.icon(
          onPressed: onPressed,
          icon: Icon(icon),
          label: Text(label),
          style: ElevatedButton.styleFrom(
            backgroundColor: color,
            foregroundColor: Colors.white,
            padding: const EdgeInsets.symmetric(vertical: 16.0, horizontal: 8.0),
            shape: RoundedRectangleBorder(
              borderRadius: BorderRadius.circular(12.0),
            ),
          ),
        ),
      ),
    );
  }
}
