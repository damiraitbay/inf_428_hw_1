import numpy as np
import unittest

# Function to convert time to cyclic features (sin and cos)
def time_to_cyclic_features(time):
    # Ensure time is within [0, 24)
    if time < 0 or time >= 24:
        raise ValueError("Time must be between 0 and 24 hours.")
    
    # Convert time to radians
    radians = 2 * np.pi * time / 24
    sin_feature = np.sin(radians)
    cos_feature = np.cos(radians)
    
    return sin_feature, cos_feature

# Function to calculate time difference correctly, considering cyclic nature
def time_difference(t1, t2):
    if t1 < 0 or t1 >= 24 or t2 < 0 or t2 >= 24:
        raise ValueError("Times must be between 0 and 24 hours.")
    
    diff = (t2 - t1) % 24
    if diff > 12:
        diff -= 24
    return diff

# Unit Tests
class TestCyclicTimeFeatures(unittest.TestCase):
    
    def test_time_to_cyclic_features(self):
        # Testing for various times in a day
        test_cases = [
            (0, (0, 1)),  # Time 00:00 should map to (0, 1)
            (6, (1, 0)),  # Time 06:00 should map to (1, 0)
            (12, (0, -1)),  # Time 12:00 should map to (0, -1)
            (18, (-1, 0)),  # Time 18:00 should map to (-1, 0)
            (23, (np.sin(2 * np.pi * 23 / 24), np.cos(2 * np.pi * 23 / 24)))  # Time 23:00, verify the calculated values
        ]
        
        for time, expected in test_cases:
            sin_feature, cos_feature = time_to_cyclic_features(time)
            self.assertAlmostEqual(sin_feature, expected[0], places=5)
            self.assertAlmostEqual(cos_feature, expected[1], places=5)
    
    def test_time_difference(self):
        # Testing time difference function
        test_cases = [
            (23, 1, 2),  # Difference between 23:00 and 01:00 should be 2 hours
            (1, 23, -2),  # Difference between 01:00 and 23:00 should be -2 hours
            (6, 18, 12),  # Difference between 06:00 and 18:00 should be 12 hours
            (15, 15, 0),  # Same time, should return 0 hours
            (5, 17, 12),  # Difference between 05:00 and 17:00 should be 12 hours
        ]
        
        for t1, t2, expected in test_cases:
            diff = time_difference(t1, t2)
            self.assertEqual(diff, expected)
    
    def test_invalid_time(self):
        # Testing for invalid time input
        with self.assertRaises(ValueError):
            time_to_cyclic_features(25)  # Invalid time > 24
            
        with self.assertRaises(ValueError):
            time_difference(25, 5)  # Invalid time > 24
            
        with self.assertRaises(ValueError):
            time_difference(5, -1)  # Invalid time < 0

# Running the test manually for Jupyter
def run_tests():
    suite = unittest.TestLoader().loadTestsFromTestCase(TestCyclicTimeFeatures)
    unittest.TextTestRunner().run(suite)

run_tests()
