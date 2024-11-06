import numpy as np
import random
import unittest

# Function to generate random threat scores based on mean, variance, and number of samples
def generate_random_data(mean, variance, num_samples):
    return np.random.randint(max(mean - variance, 0), min(mean + variance + 1, 90), num_samples)

# Function to generate random department data
def generate_random_department_data():
    departments = ['Engineering', 'Marketing', 'Finance', 'HR', 'Science']
    data = {}
    
    for dept in departments:
        num_users = random.randint(10, 200)  # Number of users between 10 and 200
        importance = random.randint(1, 5)  # Importance score between 1 and 5
        # Generate threat scores with a mean of 45 and a variance of 20 (you can adjust these parameters)
        threat_scores = generate_random_data(45, 20, num_users)
        data[dept] = {
            'users': num_users,
            'importance': importance,
            'threat_scores': threat_scores
        }
    return data

# Function to calculate aggregated threat score
def calculate_aggregated_threat_score(data):
    total_weighted_score = 0
    total_users = 0
    
    for dept, dept_data in data.items():
        num_users = dept_data['users']
        importance = dept_data['importance']
        threat_scores = dept_data['threat_scores']
        
        mean_score = np.mean(threat_scores)  # Calculate mean score for the department
        weighted_score = mean_score * importance  # Weight the score by the importance
        normalized_score = weighted_score / num_users  # Normalize by the number of users
        
        total_weighted_score += normalized_score * num_users  # Sum the weighted and normalized scores
        total_users += num_users  # Sum the total number of users
    
    return total_weighted_score / total_users  # Return the final aggregated score

# Unit Test Case
class TestThreatScoreAggregation(unittest.TestCase):
    def calculate_aggregated_threat_score(data):
       total_weighted_score = 0
       total_importance = 0  # This will help us normalize the final score
    
       for dept, dept_data in data.items():
        num_users = dept_data['users']
        importance = dept_data['importance']
        threat_scores = dept_data['threat_scores']
        
        mean_score = np.mean(threat_scores)  # Calculate the mean threat score for the department
        weighted_score = mean_score * importance  # Multiply by importance
        total_weighted_score += weighted_score * num_users  # Total weighted score, weighted by number of users
        total_importance += importance * num_users  # Total importance, weighted by number of users
    
    # Normalize the final aggregated score by the total number of users and the total importance
        return total_weighted_score / total_importance if total_importance != 0 else 0  # Avoid division by zero

    def test_random_case(self):
        # Test with randomly generated data
        data = generate_random_department_data()
        score = calculate_aggregated_threat_score(data)
        self.assertTrue(0 <= score <= 90)  # Ensure the score is between 0 and 90

    def test_case_with_high_importance(self):
        # Test with varying importance values for departments
        data = {
            'Engineering': {'users': 10, 'importance': 1, 'threat_scores': [20] * 10},
            'Marketing': {'users': 10, 'importance': 5, 'threat_scores': [40] * 10},
            'Finance': {'users': 10, 'importance': 1, 'threat_scores': [30] * 10},
            'HR': {'users': 10, 'importance': 1, 'threat_scores': [10] * 10},
            'Science': {'users': 10, 'importance': 3, 'threat_scores': [50] * 10}
        }
        # Here, Marketing and Science should have higher influence on the total score
        score = calculate_aggregated_threat_score(data)
        self.assertTrue(0 <= score <= 90)  # Ensure the score is between 0 and 90

    def test_case_with_outliers(self):
        # Test with outlier values in threat scores
        data = {
            'Engineering': {'users': 10, 'importance': 3, 'threat_scores': [90] * 10},
            'Marketing': {'users': 10, 'importance': 2, 'threat_scores': [0] * 10},
            'Finance': {'users': 10, 'importance': 4, 'threat_scores': [60] * 10},
            'HR': {'users': 10, 'importance': 1, 'threat_scores': [10] * 10},
            'Science': {'users': 10, 'importance': 3, 'threat_scores': [30] * 10}
        }
        score = calculate_aggregated_threat_score(data)
        self.assertTrue(0 <= score <= 90)  # Ensure the score is between 0 and 90

# Ensure that the test is run only if the script is executed directly
if __name__ == "__main__":
    # For interactive environments like Jupyter, use TextTestRunner()
    unittest.TextTestRunner().run(unittest.TestLoader().loadTestsFromTestCase(TestThreatScoreAggregation))
