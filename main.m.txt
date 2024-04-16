clc;
clear all;
close all;
%% main code when direct ratio not given 
magnitude1 = load('hbt_mag.txt');
magnitude2 = load('abp_mag.txt');
phase1 = load('hbt_phase.txt');
phase2 = load('abp_phase.txt');
magnitude = magnitude1/magnitude2;
phase = phase1 - phase2;
phase = phase*(pi/180);
%upto here
%% main code when direct ratio given 
magnitude = load('ratio_mag.txt');
phase = load('ratio_phase.txt');
phase = phase*(pi/180);
% upto here only
frequency_data = load('frequency_data.txt');
omega_exp = 2*pi*frequency_data;

% Convert magnitude and phase to complex numbers using Euler's formula
Hbt_exp = magnitude .* exp(1i * phase);


% Objective function to minimize
objective = @(p) sum(abs(p(1) + p(2) ./ (1 + 1i * omega_exp * p(3)) - Hbt_exp).^2);

% Initial guesses for K(a), Kv, and Tr
p0 = [0.1, 5, 20];

% Lower and upper bounds for K(a), Kv, and Tr
lb = [0.0001, 0, 0];
ub = [0.2, 10, 60];

% Optimization options
options = optimoptions('fmincon', 'Display', 'iter', 'Algorithm', 'sqp');

% Solve the optimization problem
[p_opt, fval, exitflag, output] = fmincon(objective, p0, [], [], [], [], lb, ub, [], options);

% Display optimal parameters
fprintf('Optimal K(a): %f μM/mmHg\n', p_opt(1));
fprintf('Optimal Kv: %f μM/mmHg\n', p_opt(2));
fprintf('Optimal Tr: %f s\n', p_opt(3));
