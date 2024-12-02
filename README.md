- Code developed By Rui Peleja and Rui Vieira
- Project 01 from "Artificial Intelligence Fundamentals" (MIAA)


Import the libraries

The following code imports the required library.

If dont have matplotlib install with the follow command:

!pip install matplotlib


Select the file provided and read the content

file_path = "p01_dataset_20_new.txt"
Print output if its exist:


Define the data the data for the problem.

# Call the function to get File Structure    
jobs, precedence_constraints, resources, projectExactTime = parse_job_data()

This function call "parse_job_data()" function that read the file and create the follow data:

jobs: All jobs tasks from "Duration_and_Resources"
precedence_constraints:  All jobs information from "Precedence_Relations"
resources: All jobs information from "Resource_Availability"
projectExactTime: This variable is not considerated because em some cases is wrong.
Declare the model The following code declares the model for the problem.

model = cp_model.CpModel()
Define the variables The following code defines the variables in the problem.

start_var = model.NewIntVar(0, sum(j['duration'] for j in jobs), f'start_{job_id}')
end_var = model.NewIntVar(0, sum(j['duration'] for j in jobs), f'end_{job_id}')
For each job, the program uses the model's NewIntVar method to create the variables: start_var: Start time of the job task. end_var: End time of the job task.

Define the constraints The following code defines the constraints for the problem.

# Precedences
for before, after in precedence_constraints:        
    model.Add(job_starts[after] >= job_ends[before])

# Force start with Job 1
model.Add(job_starts[1] == 0)    

# Resource
for resource, intervals_with_usage in resource_intervals.items():
    intervals_only = [i[0] for i in intervals_with_usage]
    usages = [i[1] for i in intervals_with_usage]
    model.AddCumulative(intervals_only, usages, resources[resource])
Define the objective The following code defines the objective in the problem.

model.AddMaxEquality(makespan, [job_ends[j['id']] for j in jobs]) 
model.Minimize(makespan)
Invoke the solver The following code calls the solver.

solver = cp_model.CpSolver()
status = solver.Solve(model)
Display the results The following code displays the results, including the optimal schedule and task intervals.

 # Output the results
if status == cp_model.OPTIMAL or status == cp_model.FEASIBLE:
    print(f'Best time schedule jobs (Minimized makespan): {solver.ObjectiveValue()}')
    print_job_schedule(jobs, solver, job_starts, job_ends)
# Plot the Gantt chart
The optimal schedule is shown
    plot_gantt_chart(jobs, solver, job_starts, job_ends)      
else:
    print('No feasible solution found.')
