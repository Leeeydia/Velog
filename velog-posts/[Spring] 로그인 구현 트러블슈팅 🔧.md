<h4 id="회원가입과-동일하게-로그인-응답에-제네릭을-사용하여-member--를-내려줄지-말지-헷갈렸던-문제">회원가입과 동일하게 로그인 응답에 제네릭을 사용하여 <code>&lt;Member&gt;</code>  를 내려줄지 말지 헷갈렸던 문제</h4>
<p>수정</p>
<pre><code>package com.example.demo.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import com.example.demo.service.MemberService;
import com.example.demo.util.Ut;
import com.example.demo.vo.Member;
import com.example.demo.vo.ResultData;

import jakarta.servlet.http.HttpSession;

@Controller
public class UsrMemberController {

    @Autowired
    private MemberService memberService;

    public UsrMemberController(MemberService memberService) {
        this.memberService = memberService;
    }

    @RequestMapping(&quot;/usr/member/doJoin&quot;)
    @ResponseBody
    public ResultData&lt;Member&gt; doJoin(String loginId, String loginPw, String name, String nickname, String cellphoneNum,
            String email) {

        if (Ut.isEmptyOrNull(loginId)) {
            return ResultData.from(&quot;F-1&quot;, &quot;loginId 입력해&quot;);
        }
        if (Ut.isEmptyOrNull(loginPw)) {
            return ResultData.from(&quot;F-2&quot;, &quot;loginPw 입력해&quot;);
        }
        if (Ut.isEmptyOrNull(name)) {
            return ResultData.from(&quot;F-3&quot;, &quot;name 입력해&quot;);
        }
        if (Ut.isEmptyOrNull(nickname)) {
            return ResultData.from(&quot;F-4&quot;, &quot;nickname 입력해&quot;);
        }
        if (Ut.isEmptyOrNull(cellphoneNum)) {
            return ResultData.from(&quot;F-5&quot;, &quot;cellphoneNum 입력해&quot;);
        }
        if (Ut.isEmptyOrNull(email)) {
            return ResultData.from(&quot;F-6&quot;, &quot;email 입력해&quot;);
        }

        ResultData doJoinRd = memberService.doJoin(loginId, loginPw, name, nickname, cellphoneNum, email);

        if (doJoinRd.isFail()) {
            return doJoinRd;
        }

        Member member = memberService.getMemberById((int) doJoinRd.getData1());

        return ResultData.newData(doJoinRd, member);
    }

    @RequestMapping(&quot;/usr/member/doLogin&quot;)
    @ResponseBody

    public ResultData doLogin(String loginId, String loginPw, HttpSession session) {

        boolean isLogined = false;
        if (session.getAttribute(loginedMemberId) != null) {
            isLogined = true;

        }

        if (isLogined) {
            return ResultData.from(&quot;F-A&quot;, &quot;이미 로그인 중&quot;);
        }

        if (Ut.isEmptyOrNull(loginId)) {
            return ResultData.from(&quot;F-1&quot;, &quot;loginId 입력해&quot;);
        }

        if (Ut.isEmptyOrNull(loginPw)) {
            return ResultData.from(&quot;F-2&quot;, &quot;loginPw 입력해&quot;);
        }

        int memberId = memberService.login(loginId, loginPw);

        if (memberId == -1) {
            return ResultData.from(&quot;F-3&quot;, &quot;아이디 또는 비밀번호가 틀렸습니다&quot;);
        }

        session.setAttribute(&quot;loginedMemberId&quot;, memberId);

        return ResultData.from(&quot;S-1&quot;, &quot;로그인 성공&quot;);

    }

    @RequestMapping(&quot;/usr/member/doLogout&quot;)
    @ResponseBody

    public ResultData doLogout(HttpSession session) {

    }

}
</code></pre>