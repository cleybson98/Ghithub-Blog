// src/App.jsx
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import Home from "./pages/Home";
import Post from "./pages/Post";

export default function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/post/:number" element={<Post />} />
      </Routes>
    </Router>
  );
}

// src/pages/Home.jsx
import { useEffect, useState } from "react";
import axios from "axios";
import { Link } from "react-router-dom";

const username = "octocat"; // troque pelo seu usuário
const repo = "github-blog"; // troque pelo seu repositório

export default function Home() {
  const [profile, setProfile] = useState(null);
  const [issues, setIssues] = useState([]);

  useEffect(() => {
    axios.get(`https://api.github.com/users/${username}`).then((res) => {
      setProfile(res.data);
    });

    axios.get(`https://api.github.com/repos/${username}/${repo}/issues`).then((res) => {
      setIssues(res.data);
    });
  }, []);

  return (
    <div className="p-6 max-w-3xl mx-auto">
      {profile && (
        <div className="mb-6">
          <img src={profile.avatar_url} alt="Avatar" className="w-24 rounded-full mb-2" />
          <h1 className="text-2xl font-bold">{profile.name}</h1>
          <p className="text-gray-600">{profile.bio}</p>
        </div>
      )}

      <h2 className="text-xl font-semibold mb-4">Posts</h2>
      <ul className="space-y-4">
        {issues.map((issue) => (
          <li key={issue.id} className="border p-4 rounded shadow">
            <Link to={`/post/${issue.number}`} className="text-lg font-medium text-blue-600">
              {issue.title}
            </Link>
            <p className="text-sm text-gray-500">#{issue.number} - {issue.user.login}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}

// src/pages/Post.jsx
import { useParams, Link } from "react-router-dom";
import { useEffect, useState } from "react";
import axios from "axios";

const username = "octocat"; // troque pelo seu usuário
const repo = "github-blog"; // troque pelo seu repositório

export default function Post() {
  const { number } = useParams();
  const [issue, setIssue] = useState(null);

  useEffect(() => {
    axios.get(`https://api.github.com/repos/${username}/${repo}/issues/${number}`).then((res) => {
      setIssue(res.data);
    });
  }, [number]);

  if (!issue) return <div className="p-6">Carregando...</div>;

  return (
    <div className="p-6 max-w-3xl mx-auto">
      <Link to="/" className="text-blue-500">← Voltar</Link>
      <h1 className="text-2xl font-bold mt-4">{issue.title}</h1>
      <p className="text-sm text-gray-600 mb-4">#{issue.number} criado por {issue.user.login}</p>
      <div className="prose" dangerouslySetInnerHTML={{ __html: issue.body }} />
    </div>
  );
}
