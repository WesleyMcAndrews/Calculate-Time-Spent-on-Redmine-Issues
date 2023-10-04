<!DOCTYPE html>
<html>
<body>
  <h1>How to Calculate Time Spent on Redmine Issue</h1>

  <h2>Project Description</h2>
  <p>This project aims to provide a solution for Redmine's time tracking, calculating the time spent on a issue. Redmine is a widely used project management platform. Redmine logs only the change between status, but does not compute the total time spent on a issue. Often there is a need to obtain aggregated information, such as the total time spent on a project or issue. This solution offers an easy and efficient way to perform these calculations.</p>

  <h2>Features</h2>
  <ul>
    <li><strong>Calculation of total time spent on a project:</strong> With this solution, you can quickly obtain the total time spent on a specific project or sprint, considering all the tasks related to it. This provides a better understanding of the effort put into a project and aids in future planning.</li>
    <li><strong>Calculation of total time spent on a specific issue:</strong> You can filter tasks by id or type and obtain the total time spent on each task type. This information is valuable for evaluating the team's performance in different types of activities and identifying areas for improvement.</li>
    <li><strong>Custom reports:</strong> In addition to the aforementioned features, you can create custom reports to meet the specific needs of your team or organization. Reports can include information such as time spent by user, by time period, or any other combination of desired criteria.</li>
  </ul>

  <h2>How to Use</h2>
  <ol>
    <li>Download the solution and install the necessary dependencies.</li>
    <li>Configure the connection to the Redmine database. Make sure to provide the correct information, such as username, password, and database address.</li>
	<li>Execute the SQL command to extract the information of the periods in which each issue was in attendance. It is from these periods that the calculations will be made.</li>
	<li>Import this data into a spreadsheet or your favorite BI tool. The example below uses MS-Excel. If you want to use PowerBI or QlikSense, ask me how.</li>
	<li>In the spreadsheet example on the "raw data" tab, you will find two cells to configure a business day period (Start and end of working hours). You must to discount lunch time and any other break.</li>
	<li>The last two columns contains the formula who calculate the number of hours between two datastamps (begin and end). One column show the decimal value and the other in hour:minute format. </li>
	<li>In the worksheet on the "holidays" tab, record the days that should not be considered for counting purposes. Remember that weekends (Saturdays and Sundays) are already disregarded.</li>
    <li>The last two tabs of the spreadsheet are dynamic tables which summarizes the raw data by issue id.</li>
	<li>The "exceeded" column is based on the deadline of each task.</li>
    <li>Some columns have been suppressed due to confidentiality agreement.</li>
    <li>View the results in the user interface or export them to a preferred report format.</li>
  </ol>

  <h2>System Requirements</h2>
  <ul>
    <li>Compatible operating system (Windows, Linux, macOS)</li>
    <li>A instalation of Redmine with issues registered</li>
    <li>A Redmine database. The SQL example was created for a MySQL Database, but it could easily be converted to others DBMS</li>
    <li>A google spreadsheet or MS-Excel. The example is a xlsx file from MS-Excel.</li>
  </ul>

  <h2>SQL code to extract periods in atendance</h2>
  <pre>
    <code>
    sql
    Select i.id, i.parent_id, i.subject,
	is2.name as status, u.login, sp.status_id as status_sprint, tempo.inicio, tempo.fim 
	from redmine_sistemas.issues i
	left join redmine_sistemas.custom_values cv34 on cv34.customized_id = i.id
	left join redmine_sistemas.custom_values cv35 on cv35.customized_id = i.id
	left join redmine_sistemas.custom_values cv44 on cv44.customized_id = i.id
	inner join redmine_sistemas.custom_values cv51 on cv51.customized_id = i.id
	left join redmine_sistemas.custom_values cv54 on cv54.customized_id = i.id
	inner join redmine_sistemas.issue_statuses is2 on is2.id =  i.status_id  
	left join redmine_sistemas.users u on u.id = i.assigned_to_id
	left join redmine_sistemas.issues sp on sp.id = i.parent_id
	left join
	(
		select id, inicio,
			coalesce(
				  (select min(fim) 
				   from (Select j.journalized_id as id, j.created_on as fim from redmine_sistemas.journal_details jd
	left join redmine_sistemas.journals j on j.id=jd.journal_id 
	where prop_key='status_id'
	and old_value=42
	order by id, created_on) as b 
				   where a.id = b.id 
					 and b.fim >= a.inicio 
				  ), now()) as fim
		from (Select j.journalized_id as id, j.created_on as inicio from redmine_sistemas.journal_details jd
	left join redmine_sistemas.journals j on j.id=jd.journal_id 
	where prop_key='status_id'
	and value=42
	order by id, created_on) as a 
	) as tempo on tempo.id=i.id
	order by i.id
    </code>
  </pre>

  <h2>Contribution</h2>
  <p>If you would like to contribute to this project, feel free to submit pull requests or report issues on the repository's issue page. Your contribution is highly appreciated!</p>

  <h2>License</h2>
  <p>This project is licensed under the GNU General Public License 3.0.</p>

  <h2>Contact</h2>
  <p>If you have any questions or need support, please contact me at mailto:'wesley@mcandrews.com.br'</p>

  <p>We hope this solution makes it easier to calculate the time spent on Redmine tasks and contributes to better project management. Thank you for using our solution!</p>
</body>
</html>
