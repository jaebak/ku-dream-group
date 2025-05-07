<div> 
<h3>연구과제 개발비</h3> 
총 과제 비용: <input type="text" id="project_total" placeholder="100000" size="9"> (천원) <br>
책임교수 연봉 : <input type="text" id="pi_year_earning" placeholder="8000000" size="9"> (원)<br>
책임교수 참여 비율: <input type="text" id="pi_percent" placeholder="50" size="1">  (%)<br>
과제에서 인건비 비율: <input type="text" id="person_percent" placeholder="50" size="1"> (%)<br>
<button onclick="calculate()">계산</button> 
<p id="result"></p> 
</div> 

<script> 
function calculate() { 
  const total_funding = parseFloat(document.getElementById('project_total').value); 
  const pi_year_earning = parseFloat(document.getElementById('pi_year_earning').value); 
  const pi_percent = parseFloat(document.getElementById('pi_percent').value); 
  const person_percent = parseFloat(document.getElementById('person_percent').value); 
  const pi_non_money = Math.floor(pi_year_earning * pi_percent * 0.01 * 0.001); 
  const actual_funding = total_funding * 0.8; 
  const person_funding = actual_funding * person_percent * 0.01;
  const grad_funding = Math.floor((actual_funding * person_percent * 0.01 - pi_non_money*0.2)/1.2);
  const insentive_funding = Math.floor((pi_non_money + grad_funding) * 0.2);
  const remaining_funding = actual_funding - grad_funding - insentive_funding;
  const pi_insentive_funding = Math.floor(insentive_funding * 0.7);

  document.getElementById('result').innerHTML = '<table>'
  + '<tr><td> 참여 정보 </td></tr>'
  + '<tr><td> 현금 계상률 </td> <td> 현물 계상률 </td> <td> 미지급 계상률 </td> <td> 산출근거<br>연봉금액(원) </td></tr>'
  + '<tr><td> 0 </td> <td> 0 </td> <td> ' + pi_percent + ' </td> <td> ' + pi_year_earning +  ' </td></tr>'
  + '</table> <br>'

  + '<table>'
  + '<tr><td> 정부지원금 </td> <td> 연구개발기관 부담금 </td></tr>'
  + '<tr><td> 현금 </td> <td> 현금 </td> <td> 현금 </td></tr>'
  + '<tr><td> ' + actual_funding + ' </td> <td> 0 </td> <td> 0 </td></tr>'
  + '</table> <br>'

  + '<table>'
  + '<tr><td> 세목 </td> <td> 현금 </td> <td> 현물 </td> <td> 미지급인건비 <br>계상기준금액</td> </tr>'
  + '<tr><td> 총 인건비 </td> <td> 0 </td> <td> 0 </td> <td> '+ pi_non_money+ '</td> </tr>'
  + '<tr><td> 학생인건비 </td> <td> 0 </td> <td> 0 </td> <td> - </td> </tr>'
  + '<tr><td> 통합 학생인건비(학생인건비 특례) </td> <td> '+grad_funding+' </td> <td> 0 </td> <td> - </td> </tr>'
  + '<tr><td> 남은 과제 비용 </td> <td>'+ remaining_funding+'</td><td> 0 </td> <td> - </td> </tr>'
  + '<tr><td> 연구수당 </td> <td> '+insentive_funding+' </td> <td> 0 </td> <td> - </td> </tr>'
  + '</table> <br>'
  
  + '<table>'
  + '<tr><td>교수 수당 (천원)</td> <td>' + pi_insentive_funding + '</td></tr>';
  + '</table> <br>'

  '미지급 계상률(원): ' + pi_percent + ' <br> ' 
  + '산출근거 연봉금액(원): ' + pi_year_earning + '<br>'
  + '총 인건비 (천원): 현금: 0 현물: 0 미지급인건비 계상기준금액 : ' + pi_non_money + '<br>'
  + '정부지원금 (천원): 현금: '+actual_funding+'<br>'
  + '학생인건비 (천원): 현금: 0 현물: 0 <br>'
  + '통합 학생인건비(학생인건비 특례) (천원): 현금: ' + grad_funding + ' 현물: 0 <br>'
  + '연구수당 (천원): 현금: ' + insentive_funding + ' 현물: 0 <br>'
  + '인건비 외 연구비 (천원): 현금: ' + remaining_funding + ' 현물: 0 <br>'
  + '교수 수당 (천원): ' + pi_insentive_funding + ' <br>'


}
</script>

