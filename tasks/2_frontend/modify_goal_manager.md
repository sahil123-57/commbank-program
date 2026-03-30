import { Picker } from 'emoji-mart';
import 'emoji-mart/css/emoji-mart.css';
const handleEmojiSelect = (emoji: any) => {
  setIcon(emoji.native);
};
import React, { useState } from 'react';
import { useDispatch } from 'react-redux';
import { Picker } from 'emoji-mart';
import 'emoji-mart/css/emoji-mart.css';
import { addGoal, updateGoal } from '../store/actions/goalActions'; // Adjust path if necessary
import { Goal } from '../models/goal.model';

interface Props {
  existingGoal?: Goal;
  onClose: () => void;
}

const GoalManager: React.FC<Props> = ({ existingGoal, onClose }) => {
  const dispatch = useDispatch();
  
  // State for form fields
  const [name, setName] = useState(existingGoal?.name || '');
  const [targetAmount, setTargetAmount] = useState(existingGoal?.targetAmount || 0);
  const [targetDate, setTargetDate] = useState(existingGoal?.targetDate || '');
  
  // 1. New state for the Emoji Icon
  const [icon, setIcon] = useState<string>(existingGoal?.icon || '🎯');
  const [showPicker, setShowPicker] = useState(false);

  // 2. Handler for selecting an emoji
  const handleEmojiSelect = (emoji: any) => {
    setIcon(emoji.native);
    setShowPicker(false); // Close picker after selection
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    
    const goalData = {
      ...existingGoal,
      name,
      targetAmount,
      targetDate,
      icon, // 3. Include the icon in the submitted data
    };

    if (existingGoal?.id) {
      dispatch(updateGoal(goalData));
    } else {
      dispatch(addGoal(goalData));
    }
    onClose();
  };

  return (
    <div className="goal-manager">
      <form onSubmit={handleSubmit}>
        <div className="form-group">
          <label>Goal Icon</label>
          <div className="emoji-display" onClick={() => setShowPicker(!showPicker)} style={{ cursor: 'pointer', fontSize: '2rem' }}>
            {icon}
          </div>
          
          {/* 4. The Emoji Picker Component */}
          {showPicker && (
            <div className="picker-container">
              <Picker onSelect={handleEmojiSelect} title="Pick your icon" emoji="point_up" />
            </div>
          )}
        </div>

        <div className="form-group">
          <label>Goal Name</label>
          <input 
            type="text" 
            value={name} 
            onChange={(e) => setName(e.target.value)} 
            required 
          />
        </div>

        <div className="form-group">
          <label>Target Amount</label>
          <input 
            type="number" 
            value={targetAmount} 
            onChange={(e) => setTargetAmount(Number(e.target.value))} 
            required 
          />
        </div>

        <button type="submit">{existingGoal ? 'Update Goal' : 'Create Goal'}</button>
      </form>
    </div>
  );
};

export default GoalManager;
